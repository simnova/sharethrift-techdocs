---
description: >-
  (~7 minutes) Instructions for adding Azure Monitor - App Insights logging to
  Apollo GraphQL
---

# Apollo GraphQL App Insights Configuration





#### Initialize App Insights and Import Types

Add App Insights to the data-access project:

In **terminal** go to `azure-quickstart/data-access` and issue the following command:

```bash
npm install applicationinsights
```

Add "APPINSIGHTS\_INSTRUMENTATIONKEY" to your localsettings.json with the **Instrumentation Key **from application insights

Instrumentation Key: In Azure: Go to the Application Insights resource named sharethrift. The Instrumental key will be under essentials.

{% code title="azure-quickstart/data-access/localsettings.json" %}
```javascript
{
  ...
  "Values": {
    ...
    "APPINSIGHTS_INSTRUMENTATIONKEY": "<<Your Key Here>>"
    ...
  }
  ...
}
```
{% endcode %}

Add the following **to the top **of the` index.ts` to initialize the App Insights client as early as possible and to import the types needed for the plugin. Note in the configuration we've defined a custom property of "functionArea" to help organize our logs and make them easier to query.

{% code title="azure-quickstart/data-access/graphql/index.ts" %}
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
```
{% endcode %}

**Define the Apollo Plugin**

This plugin logs some valuable information:

* **Metrics**: apollo-query / apollo-error
  * Quickly see the volume of queries / errors in Azure Monitor
* **Exceptions**:
  * Track down details of errors through Azure Log Analytics

{% code title="azure-quickstart/data-access/graphql/index.ts" %}
```typescript
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
```
{% endcode %}

**Add the plugin to Apollo Server**

{% code title="azure-quickstart/data-access/graphql/index.ts" %}
```typescript
const server = new ApolloServer({
  ...
  plugins: [
    appInsightsPlugin
  ],
  ...
});
```
{% endcode %}



Resources:

* [GraphQL Apollo Server Plugins in Typescript](https://jeffmagnusson.com/post/graphql-apollo-server-plugins-in-typescript)
* Principled GraphQL - [Structured Logging](https://principledgraphql.com/operations#9-structured-logging)
