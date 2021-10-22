---
description: Introduce Type Safety for the GraphQL Schema and Documents
---

# GraphQL Code Generator

Need to install dependencies and dev dependencies.

{% code title="/DataAccess - (Terminal / Command Propt)" %}
```bash
npm i @graphql-tools/graphql-file-loader @graphql-tools/load @graphql-tools/load-files @graphql-tools/schema @graphql-tools/stitch graphql-scalars
npm i -D @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-resolvers
npm i -D @graphql-codegen/introspection @graphql-codegen/typed-document-node @graphql-codegen/typescript-operations

```
{% endcode %}

Structure:

```bash
#inputs
/data-access
- /codegen.yml #Configuration file for GrapQL Code Generator
- /graphql
  - /context.ts #Type definition of the Context 
  - /resolvers
    - index.ts #exports all resolvers to be merged into Schema
    - /core/
      - /graphql-tools-scalars.ts #Enables Scalars to be used
      - /schema.graphql #Baseline schema
    - /**/*.reslovers.ts #GraphQL Resolvers
    - /**/*.graphql #Definitions of GraphQL Types,Queries,Mutations,Inputs
    - /types/*.ts #Definitions of TypeScript Interfaces which Match GraphQL Types


#outputs
/data-access
- /graphql
  - /generated.ts #Type file to ensure type safety for Queries/Mutations/Resolvers/Types
  - /graphql.schema.json #Introspection file to Expose all GraphQL Schema to Apollo
  
/ui/src/generated.tsx #Type file to ensure type safety for UI side Queries/Mutations/Resolvers/Types/Fragments
```

This is very simple context object - more details can be added later.

{% code title="/data-access/graphql/context.ts" %}
```typescript
export type Context = {
  authToken: string;
}
```
{% endcode %}



{% code title="/data-access/graphql/resolvers/index.ts" %}
```typescript
import {Resolvers} from '../generated';

import path  from 'path';
import { mergeResolvers } from '@graphql-tools/merge';
import { loadFilesSync } from '@graphql-tools/load-files';

const resolversArray = loadFilesSync(path.join(__dirname, "./**/*.resolvers.*"));

export const resolvers: Resolvers = mergeResolvers(resolversArray);
```
{% endcode %}



The GraphQL Tools Scalars project exposes a handful of helpful types including DateTime/Email/ObjectID. In order to make these types available we need to re-export them.

{% code title="/data-access/graphql/resolvers/core/graphql-tools-scalars.ts" %}
```typescript
const { typeDefs } = require('graphql-scalars');
const { buildSchema } = require('graphql');

const scalars = typeDefs.join('\n')

module.exports = buildSchema(scalars);
```
{% endcode %}

{% code title="/data-access/graphql/resolvers/core/schema.graphql" %}
```graphql
""" Core schema """
schema {
  query: Query
  mutation: Mutation
}

""" Base Mutation Type definition - all mutations will be defined in separate files extending this type """
type Mutation {
    """
    IGNORE: Dummy field necessary for the Mutation type to be valid
    """
    _empty:String
}

""" Base Query Type definition - , all mutations will be defined in separate files extending this type """
type Query {
    """
    IGNORE: Dummy field necessary for the Query type to be valid
    """
    _empty:String
}

""" Required to enable Apollo Cache Control """
enum CacheControlScope {
  PUBLIC
  PRIVATE
}

""" Required to enable Apollo Cache Control """
directive @cacheControl(
  maxAge: Int
  scope: CacheControlScope
  inheritMaxAge: Boolean
) on FIELD_DEFINITION | OBJECT | INTERFACE | UNION

```
{% endcode %}

{% code title="/data-access/graphql/init/extensions/schema-builder.ts" %}
```typescript
import { loadSchemaSync } from '@graphql-tools/load';
import { addResolversToSchema, mergeSchemas } from '@graphql-tools/schema';
import { resolvers } from '../../resolvers';
import { JsonFileLoader } from '@graphql-tools/json-file-loader';
import * as Scalars from 'graphql-scalars';
import { makeExecutableSchema } from '@graphql-tools/schema';


const schema = loadSchemaSync('./graphql.schema.json', {
  loaders: [new JsonFileLoader()],
});

const appSchema = addResolversToSchema(schema,resolvers)

const scalarSchema = makeExecutableSchema({
  typeDefs:[
    ...Scalars.typeDefs,
  ],
  resolvers:{
    ...Scalars.resolvers,
  }
});

export const combinedSchema = mergeSchemas({
  schemas: [
    appSchema,
    scalarSchema,
  ]
});
```
{% endcode %}

YML&#x20;

{% code title="/data-access/codegen.yml" %}
```yaml
overwrite: true
schema: 
  - "./graphql/resolvers/core/graphql-tools-scalars.ts"
  - "./graphql/resolvers/**/**.graphql"
documents: "../ui/src/components/**/**.graphql"
generates:
  graphql/generated.ts:
    config:
      contextType: "./context#Context"
      useIndexSignature: true
      mappers:
        Category: ./resolvers/types/category#CategoryType
        Listing: ./resolvers/types/listing#ListingType
        CreateListingPayload: ./resolvers/types/create-listing-payload#CreateListingPayloadType
        Location: ./resolvers/types/location#LocationType
        Point: ./resolvers/types/point#PointType
        User: ./resolvers/types/user#UserType
      scalars:
        BigInt: any
        Byte: any
        Currency: any
        Date: Date
        DateTime: any
        Duration: any
        EmailAddress: string
        GUID: string
        HSL: any
        HSLA: any
        HexColorCode: any
        Hexadecimal: any
        IBAN: any
        IPv4: any
        IPv6: any
        ISBN: any
        ISO8601Duration: any
        JSON: any
        JSONObject: any
        JWT: any
        Latitude: any
        LocalDate: any
        LocalEndTime: any
        LocalTime: any
        Long: any
        Longitude: any
        MAC: any
        NegativeFloat: any
        NegativeInt: any
        NonEmptyString: any
        NonNegativeFloat: any
        NonNegativeInt: any
        NonPositiveFloat: any
        NonPositiveInt: any
        ObjectID: any
        PhoneNumber: any
        Port: any
        PositiveFloat: any
        PositiveInt: any
        PostalCode: any
        RGB: any
        RGBA: any
        SafeInt: any
        Time: any
        Timestamp: any
        URL: any
        USCurrency: any
        UUID: any
        UnsignedFloat: any
        UnsignedInt: any
        UtcOffset: any
        Void: any
    plugins:
      - "typescript"
      - "typescript-resolvers"
  ./graphql.schema.json:
    plugins:
      - "introspection"

  ../ui/src/generated.tsx:
    config:
      withHooks: true
      withHOC: false
      withComponent: false
    plugins:
      - "typescript"
      - "typescript-operations"
      - "typed-document-node"
hooks:
  afterAllFileWrite:
    - npx prettier --write
```
{% endcode %}
