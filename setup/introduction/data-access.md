---
description: (~2 minutes)
---

# Data Access Project



Initialize the data access project:

From the `azure-quickstart` directory open a command prompt or console and issue the following commands:

```bash
func init data-access --language typescript --worker-runtime node
cd data-access
func new --template "Http Trigger" --name graphql
npm i -D typescript
npm i apollo-server-azure-functions graphql apollo-datasource-mongodb
npm install @types/node
npm i @graphql-tools/graphql-file-loader @graphql-tools/load @graphql-tools/schema graphql-scalars
npm i -D @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-resolvers
npm i -D @graphql-codegen/introspection @graphql-codegen/typed-document-node @graphql-codegen/typescript-operations
```

Launch VSCode

```bash
code .
```

VS Code should recognize that this is an Azure Functions Project, and click "Yes" to the dialog.

![](<../../.gitbook/assets/image (13).png>)

edit the .vscode/launch.json file and change the port to 9230 as [port 9090 is used by Dapr for metrics](https://docs.dapr.io/developing-applications/building-blocks/observability/metrics/), and we don't want do have each developer modify their Dapr configuration.

{% code title="azure-quickstart/data-access/.vscode/launch.json" %}
```javascript
{
 ...
 "configurations":[
   ...
   "port":9230,
   ...
 ]
 ...
}
```
{% endcode %}

edit ./tsconfig.json add the following to the _compilerOptions_ settings:

{% code title="azure-quickstart/data-access/tsconfig.json" %}
```bash
"esModuleInterop": true,
```
{% endcode %}

Replace ./graphql/index.ts with:

{% code title="azure-quickstart/data-access/graphql/index.ts" %}
```typescript
import { ApolloServer, gql }  from 'apollo-server-azure-functions';

// Construct a schema, using GraphQL schema language
const typeDefs = gql`
  type Query {
    hello: String
  }
`;

// Provide resolver functions for your schema fields
const resolvers = {
  Query: {
    hello: () => 'Hello world!',
  },
};

const server = new ApolloServer(
  { 
  typeDefs, 
  resolvers, 
  playground:true 
  },
);

exports.graphqlHandler = server.createHandler();
```
{% endcode %}

Replace ./graphql/function.json with:

{% code title="azure-quickstart/data-access/graphql/function.json" %}
```javascript
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post", "options", "put", "head", "delete", "patch"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "scriptFile": "../dist/graphql/index.js"
}
```
{% endcode %}

It is typical to only use POSTs for interactions with Apollo through the API and GETs for interacting with the playground. Apollo can also be configured to allow for GETs when interacting with the API for cached results by using [Automated Persisted Queries](https://www.apollographql.com/docs/apollo-server/performance/apq/).



#### Additional Reading:

* [Official Apollo documentation for setting up with Azure Functions](https://www.apollographql.com/docs/apollo-server/deployment/azure-functions/)
