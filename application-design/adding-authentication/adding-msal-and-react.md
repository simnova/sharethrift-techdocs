# Adding MSAL And React

Install MSAL by opening a console in UI project and installing MSAL

{% code title="/azure-quickstart/ui/" %}
```bash
npm install @azure/msal-browser
```
{% endcode %}

Modify the launch.json file of the UI project to add reference to a .env file which we can use to store secrets use for development but which we don't want to store in source control.

{% code title="/azure-quickstart/ui/launch.json" %}
```javascript
{
  ...
  "configurations": [
    {
    ...
      "envFile": "${workspaceRoot}/.env",
    ...
    }
  ]
  ...
}
```
{% endcode %}

Crate a new fie .env and place your AAD Client Secret into it

{% hint style="info" %}
**IMPORTANT!!**  Be sure to exclude .env from GIT by adding .gitignore
{% endhint %}

{% code title="/azure-quickstart/ui/.env" %}
```
REACT_APP_FUNCTION_ENDPOINT=http://localhost:7071
REACT_APP_AAD_APP_CLIENTID=<<YOUR CLIENT ID>>
REACT_APP_AAD_DIRECTORY_TENANTID=<<YOUR TENANT ID>>
REACT_APP_AAD_REDIRECT_URI=http://localhost:5000
REACT_APP_AAD_SCOPES=api://sharethrift<<random number>>.com/access_as_user
```
{% endcode %}

Now we'll add a Provider to access MSAL, this will consist of a few files:

```bash
/azure-quickstart/
- /ui/
  - /src/
    - /components/
      - /core/
        - /msal/
          - msal-context.tsx  #react context 
          - msal-provider.tsx #react provider 
          - use-msal.tsx      #expose provider methods  
```

Lets start by creating the additional directory structure:

Under : `/azure-quickstart/ui/src/`  create the following directories : `/components/core/msal`

Next let's create each of the files in `/azure-quickstart/ui/src/components/core/msal`

{% code title="msal-context.tsx" %}
```jsx
import { createContext } from 'react';

export interface MsalContextInterface {
    getAuthToken: () => Promise<string|undefined>, 
    isLoggedIn:   boolean,
    logout:       () => Promise<void>;
    login:        () => Promise<void>;
} 

const stub = (): never => {
    throw new Error('You forgot to wrap your component in <MsalProvider>.');
};

const initialContext = {
    getAuthToken:   stub,
    isLoggedIn:     false,
    logout:         stub,
    login:          stub,
};
  
const MsalContext = createContext<MsalContextInterface>(initialContext);
  
export default MsalContext;
```
{% endcode %}

{% code title="msal-provider.tsx" %}
```jsx
import React, {FC,ReactNode,useState,useEffect} from 'react';
import MsalContext from './msal-context';
import * as msal from "@azure/msal-browser";

export interface MsalMinimalSilentRequestConfig  {
    scopes: Array<string>,
    claims?:string,
    autority?:string,
    forceRquest?: boolean,
    redirectUri?: string
}

export interface MsalProviderPopupConfig {
    type: 'popup',
    msalConfig: msal.Configuration,
    silentRequestConfig:MsalMinimalSilentRequestConfig,
    endSessionRequestConfig?: msal.EndSessionRequest,
    loginRequestConfig?: msal.AuthorizationUrlRequest
}

export interface MsalProviderRedirectConfig {
    type: 'redirect',
    msalConfig: msal.Configuration,
    silentRequestConfig:MsalMinimalSilentRequestConfig,
    endSessionRequestConfig?: msal.EndSessionRequest,
    redirectRequestConfig?: msal.RedirectRequest
}

export type MsalProps = {
    config: MsalProviderPopupConfig|MsalProviderRedirectConfig;
    children:ReactNode;
}

const MsalProvider:FC<MsalProps> = (props: MsalProps) : JSX.Element => {
    const [isLoggedIn,setIsLoggedIn] = useState<boolean>(false);
    const [homeAccountId,setHomeAccountId] = useState<string>();
    var usePopup = props.config.type === 'popup';

    const msalInstance = new msal.PublicClientApplication(props.config.msalConfig);
    
    var login = async() => {
        if(usePopup){
            var popupConfig = props.config as MsalProviderPopupConfig
            await loginPopup(popupConfig.loginRequestConfig)
        }else{
            var redirectConfig = props.config as MsalProviderRedirectConfig
            await loginRedirect(redirectConfig?.redirectRequestConfig)
        }
    }

    let loginPopup = async (loginRequestConfig? : msal.AuthorizationUrlRequest) => {
        try {
            const loginResponse = await msalInstance.loginPopup(loginRequestConfig);
            setHomeAccountId(loginResponse.account.homeAccountId)
            getAuthToken()
        } catch (err) {
            setIsLoggedIn(false);
        }
    }

    let loginRedirect = async (redirectRequestConfig? :msal.RedirectRequest | undefined) => {
        try {
            await msalInstance.loginRedirect(redirectRequestConfig);
        } catch (err) {
            // handle error
        }
    }

    let getAccount =(providedHomeAccountId?:string):msal.AccountInfo | undefined => {
        let usedHomeAccountId = providedHomeAccountId??homeAccountId;
        if(!usedHomeAccountId) return undefined
        return msalInstance.getAccountByHomeId(usedHomeAccountId) ?? undefined;
    }

    let getFullSilentRequestConfig = (silentRequestConfig:MsalMinimalSilentRequestConfig, providedHomeAccountId?:string): msal.SilentRequest |undefined => {
        let account = getAccount(providedHomeAccountId)??{} as msal.AccountInfo;
        if(typeof account === 'undefined') return undefined;
        return {
            account,
            ...silentRequestConfig
        } as msal.SilentRequest
    }
    
    let getAuthToken = async (providedHomeAccountId?:string) =>{
        var fullSilentRequestConfig = getFullSilentRequestConfig(props.config.silentRequestConfig,providedHomeAccountId);
        if(!fullSilentRequestConfig) {
            setIsLoggedIn(false);
            return;
        };
        
        if(usePopup){
            var popupConfig = props.config as MsalProviderPopupConfig
            return await authTokenPopup(fullSilentRequestConfig,popupConfig.loginRequestConfig)
        }else{
            var redirectConfig = props.config as MsalProviderRedirectConfig
            return await authTokenRedirect(fullSilentRequestConfig,redirectConfig?.redirectRequestConfig)
        }
    }
    let handleRedirectResult = (authResult:msal.AuthenticationResult | null) => {
        if(!authResult || authResult.account.homeAccountId === homeAccountId) return;
        setHomeAccountId(authResult.account.homeAccountId)
        getAuthToken()
    }
    useEffect(() => {
        msalInstance.handleRedirectPromise().then(handleRedirectResult);
    },[]); // eslint-disable-line react-hooks/exhaustive-deps
    
    let authTokenPopup = async (silentRequest:msal.SilentRequest,loginRequestConfig?: msal.AuthorizationUrlRequest) : Promise<string|undefined> => {
        var authResult : msal.AuthenticationResult;
        try {
            authResult = await msalInstance.acquireTokenSilent(silentRequest)
            return authResult.accessToken;
        } catch (err) {
            if(err instanceof msal.InteractionRequiredAuthError){
                // should log in
                if(loginRequestConfig){
                    authResult = await msalInstance.acquireTokenPopup(loginRequestConfig);
                    setIsLoggedIn(true);
                    return authResult.accessToken;
                }
            }
            return undefined;
        }
    } 

    let authTokenRedirect = async (silentRequest:msal.SilentRequest,redirectRequestConfig? :msal.RedirectRequest | undefined) : Promise<string|undefined> => {
        try {
            var authResult = await msalInstance.acquireTokenSilent(silentRequest)
            setHomeAccountId(authResult.account.homeAccountId)
            setIsLoggedIn(true);
            return authResult.accessToken;
        } catch (err) {
            if(err instanceof msal.InteractionRequiredAuthError){
                // should log in
                setIsLoggedIn(false);
                if(redirectRequestConfig){
                    await msalInstance.acquireTokenRedirect(redirectRequestConfig);
                }
            }
            return undefined;
        }
    } 
 
    let logout = async() =>{
        if(props.config.endSessionRequestConfig){
            props.config.endSessionRequestConfig.account = getAccount();
        }
        await msalInstance.logout(props.config.endSessionRequestConfig)
        setIsLoggedIn(false);
    }

    return (
        <MsalContext.Provider 
            value={{
                getAuthToken:   () => getAuthToken(), 
                isLoggedIn:     isLoggedIn,
                logout:         () =>  logout(),
                login:          ()=> login(),
            }}>
            {props.children}
        </MsalContext.Provider>
    )
}

export default MsalProvider;
```
{% endcode %}

{% code title="use-msal.tsx" %}
```jsx
import { useContext } from 'react';
import MsalContext, { MsalContextInterface } from './msal-context';

const useMsal = (): MsalContextInterface => useContext(MsalContext);

export default useMsal;
```
{% endcode %}

Lets now reference this

We're going to edit `ui/src/index.tsx`

add a new import for msal, MsalProvider and the MsalProviderPopupConfig and add the config value

{% code title="/azure-quickstart/ui/src/index.tsx" %}
```jsx
/* --- other imports --- */
var clientId = process.env.REACT_APP_AAD_APP_CLIENTID??"missing-client-id";
var tenantId = process.env.REACT_APP_AAD_DIRECTORY_TENANTID??"missing-tenant-id";
var redirectUri = process.env.REACT_APP_AAD_REDIRECT_URI??"missing-redirect-uri";
var scopes = process.env.REACT_APP_AAD_SCOPES??"missing-scopes";

const commonAuthority = `https://login.microsoftonline.com/common`; //allows for anyone to register not just AAD accounts

// eslint-disable-next-line @typescript-eslint/no-unused-vars
const tenantAuthority = `https://login.microsoftonline.com/${tenantId}`; // allows ONLY for Other AAD accounts to register

const appAuthority = commonAuthority; //to allow any user to sign up must choose commonAuthority

// eslint-disable-next-line @typescript-eslint/no-unused-vars
var msalProviderPopupConfig : MsalProviderPopupConfig =  {
  type:"popup",
  msalConfig: {
    auth: {
      clientId: clientId,
      authority: appAuthority,
      redirectUri: redirectUri, 
    },
    system: {
      loggerOptions: {
        loggerCallback: (level, message, containsPii) => {
          if (containsPii) {	
            return;	
          }	
          switch (level) {	
            case msal.LogLevel.Error:	
              console.error(message);	
              return;	
            case msal.LogLevel.Info:	
              console.info(message);	
              return;	
            case msal.LogLevel.Verbose:	
              console.debug(message);	
              return;	
            case msal.LogLevel.Warning:	
              console.warn(message);	
              return;	
          }
        }
      }
    }
  },
  silentRequestConfig: {
    scopes:[scopes]
  },
  endSessionRequestConfig:{
  },
  loginRequestConfig:{
    scopes:[scopes]
  }
}

var msalProviderRedirectConfig : MsalProviderRedirectConfig =  {
  type:"redirect",
  msalConfig: {
    auth: {
      clientId: clientId,
      authority: tenantAuthority,
      redirectUri: redirectUri, 
    },
    system: {
      loggerOptions: {
        loggerCallback: (level, message, containsPii) => {
          if (containsPii) {	
            return;	
          }	
          switch (level) {	
            case msal.LogLevel.Error:	
              console.error(message);	
              return;	
            case msal.LogLevel.Info:	
              console.info(message);	
              return;	
            case msal.LogLevel.Verbose:	
              console.debug(message);	
              return;	
            case msal.LogLevel.Warning:	
              console.warn(message);	
              return;	
          }
        }
      }
    }
  },
  silentRequestConfig: {
    scopes:[scopes]
  },
  endSessionRequestConfig:{
  },
  redirectRequestConfig: {
    scopes:[scopes]
  }
}

var msalProviderConfig = msalProviderRedirectConfig; //when using Facebook Login - cannot use pop-up, login UI doesn't render correctly.
```
{% endcode %}

Wrap the **App** Component with the **MsalProvider** Component

{% code title="/azure-quickstart/ui/src/index.tsx" %}
```jsx
    <MsalProvider config={msalProviderConfig}>
      <App />
    </MsalProvider>
```
{% endcode %}

Now lets edit the `App.tsx` and use the login context:

{% code title="/azure-quickstart/ui/src/App.tsx" %}
```jsx
/* --- other imports --- */
import useMsal from "./components/core/msal/use-msal";

function App() {
  /* --- add the following line--- */
  const {login,logout,getAuthToken,isLoggedIn} = useMsal()
```
{% endcode %}



And add the following lines under the Edit and save and to reload.

{% code title="/azure-quickstart/ui/src/App.tsx" %}
```jsx
Edit <code>src/App.tsx</code> and save to reload.
{/* ------- Add the lines below ---------- */}
<br/>Login Status: {isLoggedIn?<span>Logged In</span> :<span>Logged Out</span>} <br/>
<button onClick={() => login()}>LogIn</button>
<button onClick={() => logout()}>LogOut</button>
<button onClick={() => getAuthToken()}>Get Token</button>
```
{% endcode %}

