# Google Analytics

In October 2020 Google launched **GA4** - a major change to Google Analytics, one which is more event driven to reflect that there has been a fundamental shift away from traditional web sites to web applications and mobile apps.

What follows is a guide for implementing **gtag.js** into the codebase, this is different from **Google Tag Manager.**

## **Terminology**

**Tag Manager** ( a tool for processing events in a website and sending to a GA account ) can have difficulty with the complexity of site described in this guide as it's more of a full on _javascript application_ than a website.

**GA4** -  it's wildly different than GA3 and Universal Analytics, and is the recommended path take by Google who is committed to it (as it works better with Javascript applications (like pathways), mobile apps etc).

**gtag.js** - Is a developer friendly library that exposes only a few methods for logging events later in this document each of these will be explored.



## Prerequisites

[GA4 Measurement ID ](create-analytics.md)



## How To Create The G Measurement ID

Head over to [Google Analytics](https://analytics.google.com/) and log in. (If you don't have a log in, talk to Patrick or Eddie)

Once logged in, click on **+ Create** and choose **Property**.&#x20;

Fill out the following:

* **Property name:** _<3 digit project designation>_-_\<root folder name in the mono repo>_-_\<environment>_
* **Reporting time zone**: New York Time
* **Currency:** US Dollar

Click **Next**

* Industry category: **Jobs & Education**
* Check **Large - 101 to 500 employees**

Click **Next**

Enter the website url in both the **Website URL** textbox and the **Stream name** textbox

Click **Create stream**

## Setup

### Install NPM Package

Install the [ga-4-react](https://github.com/unrealmanu/ga-4-react) npm package, it helps to ensure the that the google scripts are initialized correctly before calling functions.

This small but mighty package will do a few things:

* Reference the google script file from google's CDN
* Add a script tag to the head element to execute javascript to wire up GA
* Expose GA4 in react in an easy to use way.

{% code title="Terminal / Command Prompt ( /azure-quickstart/ui )" %}
```bash
npm install ga-4-react
```
{% endcode %}

### Add Environment Variable

Update .env file to include the GA4 Measurement ID.

{% code title="/azure-quickstart/ui/.env" %}
```bash
...
REACT_APP_GA_MEASUREMENT_ID=<<your GA4 measurement ID>>
...
```
{% endcode %}

### Initialize React Component

Update index.tsx to:

* Read the GA4 Measurement ID environment variable
* Create an instance of the Ga4react class
  * Automatically enter into debug mode when running locally&#x20;
* Initialize the ga4react object prior to rendering the app to ensure GA4 is available in the app.

{% code title="/azure-quickstart/ui/index.tsx" %}
```typescript
...
import GA4React from 'ga-4-react';

const gaMeasurementId = process.env.REACT_APP_GA_MEASUREMENT_ID as string;
const ga4react = new GA4React(gaMeasurementId,{ debug_mode: process.env.NODE_ENV !== 'production'});

(async() =>  {
  
  try{
    await ga4react.initialize();
  }catch(error){
    console.error(error);
  }

  ReactDOM.render(
    ...
  );
  
})()

...
```
{% endcode %}

### Configure CSP Header

Once deployed, the website will not work as the webpage is referencing a script from a different domain and also using some inline code, thanks to CSP.

To get things working the HTTP headers will need to be updated:

* Allow script and images to be loaded from a different domain
* Allow a very specific inline script to be run (adding a hash of the script)
  * As the inline script includes your GA Measurement ID, you have to generate the hash yourself for each environment (assuming you use a different measurement ID for QA/UAT/Prod)

&#x20;This will not work with proper security headersUpdate CSP

To generate the CSP hash use the following as an example:

{% hint style="info" %}
The script block is what gets injected on your page from the react component, to get the specific specific block for your code, pause your app in chrome debugger and get your exact string. ( /src/lib/ga4tagmanager.tsx \~line 130) (be sure to change the debug\_mode to false if you are debugging locally, as it wont be false when you deploy to your production environment)
{% endhint %}

```javascript
window.dataLayer = window.dataLayer || [];
        function gtag(){dataLayer.push(arguments);};
        gtag('js', new Date());
        gtag('config', 'G-XXXXXXXXXX', {"debug_mode":false});
```

(change out the code with your GA Measurement ID and use an [online hash generator](https://report-uri.com/home/hash) to get the proper hash value, in the example above, the hash would be: **`sha256-IBkv3CC/Fwz7jbHfcCIlP19U0yv+7XjdPbTS4DBRBeg=`**)

Read more on CSP with these resources

* [GA And CSP](https://developers.google.com/web/fundamentals/security/csp)
* [CSP Details](https://www.troyhunt.com/locking-down-your-website-scripts-with-csp-hashes-nonces-and-report-uri/)

Example CSP from a Static-Web-App config section for reference.

{% code title="/azure-quickstart/ui/staticwebapp.config.json" %}
```javascript
{
  ...
  "globalHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline' https://www.googletagmanager.com; object-src 'none'; script-src 'self' https://cdn.jsdelivr.net https://www.googletagmanager.com 'sha256-IBkv3CC/Fwz7jbHfcCIlP19U0yv+7XjdPbTS4DBRBeg='; img-src 'self' www.googletagmanager.com data:;"
  },
  ...
}
```
{% endcode %}

###

### Integrate with ReactRouter

Update App.tsx to register every page change by subscribing to react routers' location changes

{% code title="/azure-quickstart/ui/src/App.tsx" %}
```javascript
...
import React, { useEffect } from 'react';
import { Switch, Route, useLocation } from "react-router-dom";
import  { useGA4React } from "ga-4-react";

..
 
function App() {
  const ga = useGA4React();
  const location = useLocation();
  
  useEffect(()=> {
    if(ga){
      ga.pageview(location.pathname)
    }
  }, [location, ga])

  return (
    <>
      ...
    </>
  );
}

export default App;
```
{% endcode %}

### Integrate With React Components

{% hint style="info" %}
Note that "select\_content" and "click" are Google defined events each with their own valid parameters. Google has relatively low limits on the number non-standard event types and parameters therefore it is recommended to stick to the standard types and parameters.
{% endhint %}

{% code title="/azure-quickstart/ui/src/components/<<some component>>.tsx" %}
```javascript
import{ useGA4React } from "ga-4-react";


const MyComponent: FC< RouteComponentProps| any> = (props) => {
  const ga = useGA4React();

  var someAction = (eventDetail:string) => {
    if(ga && eventDetail ){
      ga.gtag(
        'event',
        'select_content',
        {
          'content_type':'user-took-some-action',
          'item_id': eventDetail
        }
      );
    }
  }
  
  const trackOutboundLink = (event:any) => {
    var targetUrl = event.target.href;
    if(ga && targetUrl ){
      ga.gtag(
        'event',
        'click',
        {
          'page_location': targetUrl,
          'page_referrer': document.location.pathname + document.location.search
        });
    }
  }
  
  ...
  
  return(
    <>
        ...
          <a href='https://www.google.com' target='_blank' rel='noreferrer'  onClick={trackOutboundLink}>Google</a>
        ...
    </>
  )
}

export default withRouter(MyComponent);
```
{% endcode %}

### Adding Helmet to update Page Name&#x20;

It is always good to update the page title for usability, it also benefits in GA

{% code title="Terminal / Command Prompt ( /azure-quickstart/ui )" %}
```bash
npm install react-helmet
```
{% endcode %}

### Integrate Helmet with your React Pages

{% code title="/azure-quickstart/ui/src/components/<<some component>>.tsx" %}
```bash
import React, { FC } from "react";
import { Helmet } from 'react-helmet';
...


const MyComponent: FC<any> = (props) => {
  ...

  return(
    <>
        <Helmet>
          <title>A PAGE TITLE FOR THIS COMPONENT</title>
        </Helmet>
        ... YOUR NORMAL REACT CODE ...
    </>
  )
}
```
{% endcode %}



### Verifying with Debug Mode

Testing everything out:

Debug the codebase and navigate to the webpage, a new floating box will be shown the lower right of the screen, click "connect" to connect to the Tag Assistant, a new browser window will open and from there it is easy to see google is receiving from the website.



## Additional Resources

* Google Analytics - [Events Reference](https://support.google.com/analytics/topic/9756175?hl=en\&ref\_topic=9143232)
* Google Analytics - [Advice on using Events](https://support.google.com/analytics/answer/10106314?hl=en\&ref\_topic=10737980)&#x20;
* Google Tag Assistant - [Introduction and Help](https://support.google.com/tagassistant/answer/10039345)
