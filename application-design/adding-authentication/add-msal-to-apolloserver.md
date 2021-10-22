# Add MSAL to ApolloServer



Install libraries to validate tokens by opening a console in **data-access** project and issuing the following commands

{% code title="/azure-quickstart/data-access/" %}
```bash
npm install jose
npm install openid-client
```
{% endcode %}

In  `/azure-quickstart/data-access/` create the directories `sharedCode/auth` .

In the `auth` directory, create a new file `msal.ts` and add the following code.

{% code title="/azure-quickstart/data-access/shared-code/auth/msal.ts" %}
```typescript
import { Issuer, Client } from 'openid-client';
import {JWT} from 'jose';

var verifyAccessToken = async (context) : Promise<[object, boolean]>  => {
  let token = context.request.headers["authorization"];
  if (!token || !token.startsWith("Bearer ")) return [{}, false];

  token = token.slice(7, token.length).trimLeft(); // Remove 'Bearer ' characters from start of Auth header value

  const settings = {
    audience: process.env.AAD_TOKEN_APPLICATION_ID,
    openIdConfigUrl: process.env.AAD_TOKEN_OPEN_ID_CONNECT_METADATA_DOCUMENT,
    tenantId: process.env.AAD_TOKEN_TENANT_ID
  };

  const issuer = await Issuer.discover(settings.openIdConfigUrl);
  const keyStore = await issuer.keystore();

  var results = JWT.verify(
    token,
    keyStore, 
    {
      audience: settings.audience,
     // issuer: 
        //issuer must remain commented out if you're accepting tokens from :
        // Microsoft's public endpoint (which will be: 'https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0' or it can be any AAD tenant's ID)
        //if you only want to accept local AAD Accounts use: `https://login.microsoftonline.com/${settings.tenantId}/v2.0`
    }
  );
  
  return [
    {
      "authToken": results,
    },
    true
  ];
}

export default {
  VerifyAccessToken: verifyAccessToken
}
```
{% endcode %}

Replace the contents of the main function file with the following to properly reference MSAL and to allow for auth headers.

{% code title="/azure-quickstart/data-access/graphql/index.ts" %}
```typescript
let appInsights = require("applicationinsights");
appInsights.setup().start();
appInsights.defaultClient.commonProperties = {
  environment: process.env.WEBSITE_HOSTNAME,
  functionArea: "graphql"
};
let appInsightsClient = appInsights.defaultClient;

import {
  ApolloServerPlugin,
  GraphQLRequestContext,
  GraphQLRequestListener,
} from 'apollo-server-plugin-base';

import { ApolloServer, gql }  from 'apollo-server-azure-functions';
import MsalAuth from '../shared/auth/msal';
import { HttpRequest, Context } from '@azure/functions';

// Construct a schema, using GraphQL schema language
const typeDefs = gql`
  type Query {
    hello: String
  }
`;

// Provide resolver functions for your schema fields
const resolvers = {
  Query: {
    hello: (parent,args,context) => {
      return `Hello world! ${JSON.stringify(context)}`
    },
  },
};

// referenced from https://jeffmagnusson.com/post/graphql-apollo-server-plugins-in-typescript
const appInsightsPlugin = <ApolloServerPlugin & GraphQLRequestListener>{

  // Fires whenever a GraphQL request is received from a client.
  requestDidStart(requestContext:GraphQLRequestContext): GraphQLRequestListener | void{
    appInsightsClient.trackMetric({name: "apollo-query", value: 1});
    return this;
  },
  // Fires for graph exceptions
  didEncounterErrors: function(requestContext:GraphQLRequestContext) {
    appInsightsClient.trackMetric({name: "apollo-error", value: 1});
    appInsightsClient.trackException({exception: new Error("Apollo Error")});
    appInsightsClient.trackException({exception: {category:"Apollo Error", details: requestContext.errors}});
  }
  
}

const getPlaygroundSetting = () => {
  if(process.env.APOLLO_PLAYGROUND_VISIBLE === "true" ){
    if(process.env.APOLLO_PLAYGROUND_ENDPOINT){
      return {endpoint :process.env.APOLLO_PLAYGROUND_ENDPOINT}
    }
    return true
  } else {
    return false
  }
}

const server = new ApolloServer(
  { 
    typeDefs, 
    resolvers, 
    playground: {endpoint:process.env.APOLLO_PLAYGROUND_ENDPOINT},
    plugins: [
      appInsightsPlugin
    ],
    context: async (request) => {
      var [ user, validated ] = await MsalAuth.VerifyAccessToken(request);
      return {user, validated};
    },
  },
);

const graphqlHandler = server.createHandler({
  cors: {
    origin: '*',
    credentials: true,
  },
});

export default (context: Context, req: HttpRequest) => {
  // https://github.com/Azure/azure-functions-host/issues/6013
  req.headers['x-ms-privatelink-id'] = '';
  // apollo-server only reads this specific string
  req.headers['Access-Control-Request-Headers'] = req.headers['Access-Control-Request-Headers'] || req.headers['access-control-request-headers'];
  return graphqlHandler(context, req);
}
```
{% endcode %}

Update the local settings to supply the function with the appropriate values:

{% code title="/azure-quickstart/data-access/local.settings.json" %}
```typescript
{
  ...
  "Values": [
    {
    ...
    "AAD_TOKEN_OPEN_ID_CONNECT_METADATA_DOCUMENT" : "https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration",
    "AAD_TOKEN_APPLICATION_ID" : "<< AAD API APPPLICATION IDENTIFIER>>",
    "AAD_TOKEN_TENANT_ID" : "<< AAD API TENANT ID>>",
    
    "APOLLO_PLAYGROUND_URI" : "http://localhost:7071/api/graphql"
    ...
    }
  ]
  ...
}
```
{% endcode %}

Azure Settings

Open the data-access function App

* In the function app navigate to Configuration&#x20;
  * Choose **+ New Application Setting** for each of the following
    * Name: AAD\_TOKEN\_OPEN\_ID\_CONNECT\_METADATA\_DOCUMENT
      * Value: <\<SAME VALUE AS USED IN LOCAL SETTINGS>>
    * Name: AAD\_TOKEN\_APPLICATION\_ID
      * Value: <\<SAME VALUE AS USED IN LOCAL SETTINGS>>
    * Name: AAD\_TOKEN\_TENANT\_ID
      * Value: <\<SAME VALUE AS USED IN LOCAL SETTINGS>>
    * Name: APOLLO\_PLAYGROUND\_URI
      * Value: <\<AZURE FRONT DOOR URL>>/api/graphql \
        (e.g.: https://sharethrift<\<random number>>.azurefd.net/api/graphql)
* Choose** Save**
* Choose** Continue **(wait until completion)

Repeat the same steps for the data-access-west function app.

### Important Warnings

{% hint style="warning" %}
**Double Calls to Apollo?** With React.StrictMode enabled (a good thing) you will notice double calls to Apollo ([source](https://github.com/apollographql/apollo-client/issues/6037#issuecomment-659630298)) this is actually intended by the React team ([source](https://reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects)) to help identify issues to prepare you for React Concurrent, and likely the Apollo team may make changes to better prepare for this. **When you compile for production you likely won't see this behavior.**
{% endhint %}
