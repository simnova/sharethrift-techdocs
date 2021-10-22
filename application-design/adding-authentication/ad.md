# Add MSAL to ApolloClient

Begin by creating a new react component to wire in ApolloClient authenticated with MSAL



Start by creating a new component:

{% code title="/azure-quickstart/ui/src/components/core/apollo-connection.tsx" %}
```typescript
import React, { FC,useEffect } from 'react';
import App from '../../App';
import {
  ApolloClient,  
  ApolloProvider,
  createHttpLink,
  InMemoryCache,
  from
} from '@apollo/client';
import {setContext} from '@apollo/client/link/context';
import useMsal from './msal/use-msal';

const ApolloConnection: FC<any> = () => {
  const { getAuthToken, isLoggedIn } = useMsal();

  const withToken = setContext(async(_,{headers}) =>{
    const token = await getAuthToken();
    return{
      headers: {
        ...headers,
        Authorization: token ? `Bearer ${token}` : null
      }
    }
  }) 
 
  const httpLink = createHttpLink({
    uri: `${process.env.REACT_APP_FUNCTION_ENDPOINT}/api/graphql`
  });

  const client = new ApolloClient({
    link: from([withToken, httpLink]),
    cache: new InMemoryCache(),
  });

  useEffect(() => {
    if(!isLoggedIn){
      (async () => {
        await client.resetStore() //clear Apollo cache when user loggs off
      })()
    }
  },[isLoggedIn]) // eslint-disable-line react-hooks/exhaustive-deps

  return (
    <ApolloProvider client={client}>
      <App />
    </ApolloProvider>
  );
};

export default ApolloConnection;
```
{% endcode %}

Add an import for the new **ApolloConnection** component:

{% code title="/azure-quickstart/ui/src/index.tsx" %}
```typescript
/** other imports **/
import ApolloConnection from './components/core/apollo-connection';
```
{% endcode %}

Change the component: `<App />` to `<ApolloConnection />` so that it looks as it does below:

{% code title="/azure-quickstart/ui/src/index.tsx" %}
```typescript
    <MsalProvider config={msalProviderConfig}>
      <ApolloConnection />
    </MsalProvider>
```
{% endcode %}





