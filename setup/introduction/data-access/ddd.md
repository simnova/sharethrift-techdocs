# DDD

<pre data-title="Terminal Window"><code><strong>cd /Volumes/files/src
</strong>git clone pathways repo
cd pathways
func init data-access --language typescript --worker-runtime node
cd data-access
func new --template "Http Trigger" --name graphql
npm i -D typescript
npm i apollo-server-azure-functions graphql@15 apollo-datasource-mongodb apollo-server-plugin-response-cache
npm install @types/node
npm i @graphql-tools/graphql-file-loader @graphql-tools/load @graphql-tools/schema graphql-scalars
npm i -D @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-resolvers
npm i -D @graphql-codegen/introspection @graphql-codegen/typed-document-node @graphql-codegen/typescript-operations
npm i @graphql-tools/load-files @graphql-tools/schema @graphql-tools/stitch @graphql-tools/json-file-loader
npm i graphql-fields graphql-middleware graphql-mongo-fields graphql-shield
npm i -D mongodb-memory-server
npm i -D npm-run-all
npm i -D jest  ts-jest @types/jest
npm i mongoose nanoid
npm i jose openid-client
npm i dayjs
npm i @lucaspaganini/value-objects
npm i   @azure/arm-maps @azure/cognitiveservices-contentmoderator @azure/identity @azure/ms-rest-azure-js @azure/search-documents @azure/storage-blob</code></pre>

{% code title="package.json" %}
```
  "scripts": {
    "build": "tsc",
    "build:production": "npm run prestart && npm prune --production",
    "watch": "tsc --w",
    "prestart": "npm run build && func extensions install",
    "start:host": "func start",
    "start": "npm-run-all --parallel start:host watch",
    "test": "npx jest --watchAll=true",
    "gen": "graphql-codegen --config codegen.yml",
    "gen:watch": "graphql-codegen --config codegen.yml --watch  --silent=false"
  },
```
{% endcode %}

{% code title="tsconfig.json" %}
```
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es6",
    "outDir": "dist",
    "rootDir": ".",
    "sourceMap": true,
    "strict": false,
    "esModuleInterop": true,
    "resolveJsonModule": true,
  }
}
```
{% endcode %}

{% code title="host.json" %}
```
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "excludedTypes": "Request"
      }
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[3.3.0, 4.0.0)"
  }
}
```
{% endcode %}

{% code title=".vscode/launch.json" %}
```
{
    "version": "0.2.0",
    "configurations": [

        {
            "name": "Attach to Node Functions",
            "type": "node",
            "request": "attach",
            "port": 9229,
            "preLaunchTask": "func: host start"
        }
,
        {
            "type": "node",
            "request": "attach",
            "name": "Jest Tests",
            "preLaunchTask": "test",
        }
    ]
}
```
{% endcode %}

{% code title=".vscode/setting.json" %}
```
{
    "azureFunctions.deploySubpath": ".",
    "azureFunctions.postDeployTask": "npm install (functions)",
    "azureFunctions.projectLanguage": "TypeScript",
    "azureFunctions.projectRuntime": "~4",
    "debug.internalConsoleOptions": "neverOpen",
    "azureFunctions.preDeployTask": "npm prune (functions)",
    "editor.tabSize": 2,
    "cSpell.words": [
        "datasource",
        "lucaspaganini",
        "maxlength"
    ]
}
```
{% endcode %}

{% code title=".vscode/tasks.json" %}
```
{
	"version": "2.0.0",
	"tasks": [
		{
			"type": "func",
			"command": "host start",
			"problemMatcher": "$func-node-watch",
			"isBackground": true,
			"dependsOn": "npm build (functions)"
		},
		{
			"type": "shell",
			"label": "npm build (functions)",
			"command": "npm run build",
			"dependsOn": "npm install (functions)",
			"problemMatcher": "$tsc"
		},
		{
			"type": "shell",
			"label": "npm install (functions)",
			"command": "npm install"
		},
		{
			"type": "shell",
			"label": "test",
			"command": "npx jest --watchAll=true",
			"dependsOn": "npm build (functions)"
		},
		{
			"type": "shell",
			"label": "npm prune (functions)",
			"command": "npm prune --production",
			"dependsOn": "npm build (functions)",
			"problemMatcher": []
		}
	]
}
```
{% endcode %}

{% code title=".prettierrc" %}
```
{ 
  "trailingComma": "es5", 
  "tabWidth": 2, 
  "semi": true, 
  "singleQuote": true, 
  "jsxSingleQuote": true, 
  "printWidth": 200 
}
```
{% endcode %}

{% code title="codegen.yml" %}
```
# @format

overwrite: true
schema:
  - './graphql/resolvers/core/graphql-tools-scalars.ts'
  - './graphql/resolvers/**/**.graphql'
documents: '../ui/src/components/**/**.graphql'
generates:
  graphql/generated.ts:
    config:
      contextType: './context#Context'
      useIndexSignature: true

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
      - 'typescript'
      - 'typescript-resolvers'
  ./graphql.schema.json:
    plugins:
      - 'introspection'
  ../ui/src/generated.tsx:
    config:
      withHooks: true
      withHOC: false
      withComponent: false
    plugins:
      - 'typescript'
      - 'typescript-operations'
      - 'typed-document-node'

hooks:
  afterAllFileWrite:
    - npx prettier --write

```
{% endcode %}

{% code title="graphql/function.json" %}
```
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "route": "graphql/{*segments}",
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

{% code title="graphql/context.ts" %}
```
import { Passport } from '../domain/contexts/iam/passport';
import { DataSourcesType } from './data-sources';

export type Context = {
  verifiedUser: {
    verifiedJWT: VerifiedJWT;
    openIdConfigKey: string;
 
  };
  passport: Passport;
  dataSources: DataSourcesType;
  executionContext: any;
}

export type VerifiedJWT = {
  oid: string;
  accountType: string;
}

```
{% endcode %}

{% code title="graphql/index.ts" %}
```
import { ApolloServerRequestHandler } from './init/apollo';
import { HttpRequest, Context } from "@azure/functions";

let apolloServerRequestHandler = new ApolloServerRequestHandler(
  new Map<string,string>([
    ["CaseManagementPortal","CASE_MANAGEMENT_PORTAL"]
  ])
);

// Exexute the following with every http request
export default (context: Context, req: HttpRequest) => {
  return apolloServerRequestHandler.handleRequests(context, req);
}
```
{% endcode %}

In the above codeblock, edit the Map entries as per business needs



{% code title="graphql/init/extensions/passport-context.ts" %}
```
import { HttpRequest } from "@azure/functions";
import { PassportImpl, Passport, ReadOnlyPassport } from "../../../domain/contexts/iam/passport";
import { Context } from "../../context";
import {Account, AccountModel} from '../../../infrastructure/data-sources/cosmos-db/models/account'
import {AccountConverter} from '../../../domain/infrastructure/persistence/account.domain-adapter'

export const decorateContext = async (context: Partial<Context>, req:HttpRequest): Promise<void> => {
  let oid = context.verifiedUser.verifiedJWT.oid;
  let accountType = context.verifiedUser.verifiedJWT.accountType;
  let readOnlyPassport = ReadOnlyPassport.GetInstance();
  if(oid && accountType) {
    let account: Account = await AccountModel.findOne({oid, accountType});
    if(account) {
      let accountDo = new AccountConverter().toDomain(account, {passport: readOnlyPassport});
      context.passport = new PassportImpl(accountDo);
    }
  }
  else {
    context.passport = readOnlyPassport;
  }
}
```
{% endcode %}

{% code title="graphql/init/extensions/portal-token-validation.ts" %}
```
import { OpenIdConfig, VerifiedTokenService } from './verified-token-service';

 export class PortalTokenValidation {
  private tokenVerifier: VerifiedTokenService;
  private tokenSettings: Map<string,OpenIdConfig>;
  
  /**
   * @param refreshInterval The number of seconds to wait between refreshing the keystore, defaults to 5 minutes
   * @param openIdConfigs A map of key and enviornment variable prefix, when a JWT is verified, the matching key is returned with the verified JWT
   * 
   * Expects to have 3 environment variables set:
   * - [prefix]_OIDC_ENDPOINT
   * - [prefix]_OIDC_AUDIENCE  
   * - [prefix]_OIDC_ISSUER
   **/
  constructor(portal: Map<string, string>, refreshInterval: number  = 1000*60*5) {
    this.tokenSettings = new Map<string,OpenIdConfig>();
    
    for(let [portalKey, envPrefix] of portal){
      this.tokenSettings.set(
        portalKey,
        {
          oidcEndpoint: this.tryGetConfigValue(envPrefix + '_OIDC_ENDPOINT'),    
          audience: this.tryGetConfigValue(envPrefix + '_OIDC_AUDIENCE'),
          issuerUrl: this.tryGetConfigValue(envPrefix + '_OIDC_ISSUER')
        } as OpenIdConfig
      );
    }
    this.tokenVerifier = new VerifiedTokenService(this.tokenSettings,refreshInterval);
  }

  public tryGetConfigValue(configKey:string){
    if(process.env.hasOwnProperty(configKey)){
      return process.env[configKey];
    }else{
      throw new Error(`Environment variable ${configKey} not set`);
    }
  }

  public Start(){
    this.tokenVerifier.Start();
  }

  public async GetVerifiedUser (bearerToken:string): Promise<{verifiedJWT:any,openIdConfigKey:string}|null>{ 
    for await(let [openIdConfigKey] of this.tokenSettings){
      let verifedJWT = await this.tokenVerifier.GetVerifiedJwt(bearerToken,openIdConfigKey);
      console.log(`for ${openIdConfigKey} with bearerToken: ${bearerToken} verifiedJWT: ${JSON.stringify(verifedJWT)}`)
      if(verifedJWT){
        return {
          verifiedJWT:verifedJWT.payload,
          openIdConfigKey:openIdConfigKey
        }
      }
    }
    return null;
  }

}
```
{% endcode %}

{% code title="graphql/init/extensions/verified-token-service.ts" %}
```
import { jwtVerify, createRemoteJWKSet,  JWSHeaderParameters, FlattenedJWSInput } from 'jose';
import { GetKeyFunction, JWTVerifyResult, ResolvedKey } from 'jose/dist/types/types';
import { Issuer } from 'openid-client';

export type OpenIdConfig = {
  issuerUrl:string;
  oidcEndpoint:string;
  audience: any;
  /**
   * The number of seconds to allow the current time to be off from the token's, 
   * (defalts to 5 minutes if not specified)
   */
  clockTolerance?: string;
  ignoreNbf?: boolean;
}

export class VerifiedTokenService  {
  openIdConfigs: Map<string,OpenIdConfig>;
  refreshInterval:number;
  keyStoreCollection:  Map<string,{keyStore: GetKeyFunction<JWSHeaderParameters, FlattenedJWSInput>, issuerUrl: string}>;
  timerInstance:NodeJS.Timer;

  /**
   * @param openIdConfigs A map of key to OpenIdConfig, when a JWT is verified, the matching key is returned with the verified JWT
   * @param refreshInterval The number of seconds to wait between refreshing the keystore, defaults to 5 minutes
   **/
  constructor(openIdConfigs:Map<string,OpenIdConfig>, refreshInterval:number = 1000*60*5) {
    if(!openIdConfigs) {throw new Error('openIdConfigs is required');}
    this.keyStoreCollection = new Map<string,{keyStore: GetKeyFunction<JWSHeaderParameters, FlattenedJWSInput>, issuerUrl: string}>();
    this.openIdConfigs = openIdConfigs;
    this.refreshInterval = refreshInterval;
  }

  /**
   * 
   * Refresh the keystore collection periodically
   * 
   **/
  public Start() {
    console.log('Starting VerifiedTokenService');
    if(this.timerInstance) {
      return; // already running
    }
    //need to run immediately...
    (async () => {
      await this.refreshCollection();
    })();
    //..as setInterval only runs after the timer runs out
    this.timerInstance = setInterval(() =>  {
      (async () => {
      await this.refreshCollection();
      })();
    }, this.refreshInterval);
  }

  /**
   * For each OIDC Endpoint, either create a new keystore or refresh the existing one.
   * Keys in the keystore expire over time, so it is important to refresh the keystore periodically
   */
  async refreshCollection() {
    if(!this.openIdConfigs){return}
    for(let configKey of  [...this.openIdConfigs.keys()]) {
      let newKeyStore = {
        keyStore: createRemoteJWKSet(new URL(this.openIdConfigs.get(configKey).oidcEndpoint)),
        issuerUrl:  this.openIdConfigs.get(configKey).issuerUrl
      }
      if(newKeyStore) {
        if(this.keyStoreCollection.has(configKey)) {
          this.keyStoreCollection.delete(configKey); // remove old keystore if it exists
        }
        this.keyStoreCollection.set(configKey, newKeyStore); //Update keystore with new one or add it if it doesn't exist
      }
    }
  }
  


  public async GetVerifiedJwt(bearerToken:string, configKey:string) : Promise<JWTVerifyResult & ResolvedKey> {
    if(!this.timerInstance) {
      throw new Error('ContextUserFromMsal not started');
    }
    if(!this.keyStoreCollection.has(configKey)) {
      throw new Error('Invalid OpenIdConfig Key');
    }
    let openIdConfig = this.openIdConfigs.get(configKey);
    return jwtVerify(
      bearerToken,
      this.keyStoreCollection.get(configKey).keyStore,
     // createRemoteJWKSet(new URL(this.openIdConfigs.get(configKey).oidcEndpoint)), 
      {
        audience: openIdConfig.audience,
        issuer: openIdConfig.issuerUrl,
        //ignoreNbf: openIdConfig.ignoreNbf??true,
        clockTolerance: openIdConfig.clockTolerance?? '5 minutes',
      }
    )
  }

}
```
{% endcode %}

{% code title="graphql/init/extensions/util.ts" %}
```
import { HttpRequest } from '@azure/functions';

/**
 * Extracts the bearer token from the request assuming it is in the Authorization header and starts with 'Bearer '
 * @param request The request to extract the token from
 * @returns The token or null if it could not be extracted
 */
export const ExtractBearerToken = (request: HttpRequest): string => {
  let token = request.headers['authorization'];
  if (!token || !(token.startsWith('Bearer '))) {
    return null;
  }

  // Remove Bearer from string
  token = token.slice(7, token.length).trimLeft();
  return token;
}
```
{% endcode %}

{% code title="graphql/init/extensions/schema-builder.ts" %}
```
/**
 * This file merges all the schemas together to create 
 * the overall Apollo schema
 */

import { loadSchemaSync } from '@graphql-tools/load';
import { addResolversToSchema, mergeSchemas, makeExecutableSchema } from '@graphql-tools/schema';
import { resolvers } from '../../schema';
import { JsonFileLoader } from '@graphql-tools/json-file-loader';
import * as Scalars from 'graphql-scalars';

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

{% code title="graphql/init/apollo.ts" %}
```
import { ApolloServer, CreateHandlerOptions } from 'apollo-server-azure-functions';
import { HttpRequest, Context } from '@azure/functions';
import { DataSources } from '../data-sources';
import { connect } from '../../infrastructure/data-sources/cosmos-db/connect';
import { GraphQLServiceContext } from 'apollo-server-types';
import responseCachePlugin from 'apollo-server-plugin-response-cache';
import mongoose from 'mongoose';
import { PortalTokenValidation } from './extensions/portal-token-validation';
import { combinedSchema } from './extensions/schema-builder';
import * as util  from './extensions/util';
import RegisterHandlers from '../../domain/infrastructure/event-handlers/index'
import { Context as ApolloContext } from '../context';
import { applyMiddleware } from 'graphql-middleware'
import { permissions } from '../schema';
import { GraphQLSchemaWithFragmentReplacements } from 'graphql-middleware/dist/types';

import {
  GraphQLRequestContext,
} from 'apollo-server-plugin-base'
import { decorateContext } from './extensions/passport-context';

export class ApolloServerRequestHandler {


    
  private readonly serverConfig = (
    portalTokenExtractor:PortalTokenValidation,
    securedSchema:GraphQLSchemaWithFragmentReplacements) => {
    return {
      schema:securedSchema,

      context: async (req:any) => { //context loads before data sources
        let bearerToken = util.ExtractBearerToken(req.request);
        let context:Partial<ApolloContext> ={};
        
        bearerToken = (process.env.DO_VERIFY_TOKEN === 'true') ? bearerToken : 'fake-token'; 
        if(bearerToken){
          // let verifiedUser = await portalTokenExtractor.GetVerifiedUser(bearerToken);
          let verifiedUser = (process.env.DO_VERIFY_TOKEN === 'true') ? (
            await portalTokenExtractor.GetVerifiedUser(bearerToken)) : {
              verifiedJWT: {
                oid: '123xyz',
                accountType: 'staff'
              },
              openIdConfigKey:'ApplicantPortal'
            };


          console.log('Decorating context with verified user:',JSON.stringify(verifiedUser));
          if(verifiedUser){
            context.verifiedUser = verifiedUser
            console.log('context value is now:', JSON.stringify(context));
          }
        }
        await decorateContext(context,req.request); 
        return context;
      },
      dataSources: () => {
        return DataSources
      },
    //  playground: { endpoint: '/api/graphql/playground' },
      plugins:[
        {
          async didEncounterErrors (requestContext: GraphQLRequestContext) {
            console.error('Apollo Server encountered error:', requestContext.errors);
          },
          async serverWillStart(service: GraphQLServiceContext) {
            console.log('Apollo Server Starting');
            await connect();
            portalTokenExtractor.Start();
        
            RegisterHandlers();
          },
        },
        responseCachePlugin()
      ]
    }
  };

  public handleRequests(context: Context, req: HttpRequest){
    req.headers['x-ms-privatelink-id'] = ''; // https://github.com/Azure/azure-functions-host/issues/6013
    req.headers['server'] = null; //hide microsoft server header
    return this.graphqlHandlerObj(context, req)
  }
  
  private readonly graphqlHandlerObj:any;

  constructor(portals:Map<string,string>){
    console.log(' -=-=-=-=-=-=-=-=-= INITIALIZING APOLLO -=-=-=-=-=-=-=-=-=')
    const portalTokenExtractor:PortalTokenValidation = new PortalTokenValidation(portals);

    const server = new ApolloServer({
      ...this.serverConfig(portalTokenExtractor,
      combinedSchema)
    });

    this.graphqlHandlerObj = server.createHandler({
      cors: {
        origin: true,
        credentials: true,
      },

      // health check endpoint is: https://<function-name>.azurewebsites.net/api/graphql/.well-known/apollo/server-health
      onHealthCheck: async (): Promise<any> => {
        // doesn't work yet 
        // https://github.com/apollographql/apollo-server/pull/5270
        // https://github.com/apollographql/apollo-server/pull/5003
        let mongoConnected = mongoose.connection.readyState === 1;
        if(mongoConnected) {
          return;
        } else {
          throw new Error('MongoDB is not connected');
        }
      },
    } as CreateHandlerOptions)
  }

}
```
{% endcode %}

{% code title="graphql/data-sources/index.ts" %}
```
/** @format */

import { CognitiveSearch } from "./cognitive-search";
import { CosmosDB } from "./cosmos-db";
import { Domain } from "./domain";

export const DataSources = {
  ...CosmosDB,
  ...Domain,
  ...CognitiveSearch,
};

export type DataSourcesType = typeof DataSources;

```
{% endcode %}

{% code title="graphql/schema/core/base.graphql" %}
```
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
directive @cacheControl22(
  maxAge: Int
  scope: CacheControlScope
  inheritMaxAge: Boolean
) on FIELD_DEFINITION | OBJECT | INTERFACE | UNION
```
{% endcode %}



{% code title="graphql/schema/core/graphql-tools-scalars.ts" %}
```
/* ensure these remain as require statements as they get called from graphql-code-generator */
const { typeDefs } = require('graphql-scalars');
const { buildSchema } = require('graphql');

const scalars = typeDefs.join('\n')

module.exports = buildSchema(scalars);
```
{% endcode %}

{% code title="graphql/schema/interfaces/mutation-result.graphql" %}
```
interface MutationResult {
  status: MutationStatus!
}

```
{% endcode %}

{% code title="graphql/schema/shared/blob-auth-header.graphql" %}
```
type BlobAuthHeader {
  authHeader: String
  blobName: String
  blobContainer: String
  requestDate: String
}
```
{% endcode %}

{% code title="graphql/schema/shared/mutation-status.graphql" %}
```
type MutationStatus {
  success: Boolean!
  errorMessage: String
}
```
{% endcode %}

{% code title="graphql/schema/index.ts" %}
```
/**
 * This file is used to traverse  all the files in this directory
 * and merge them together to create the application schema
 */

import { Resolvers } from '../generated';
import path from 'path';
import { mergeResolvers } from '@graphql-tools/merge';
import { loadFilesSync } from '@graphql-tools/load-files';

console.log(`Loading resolvers from ${path.join(__dirname, "./**/*.resolvers.*")}`);
const resolversArray = loadFilesSync(path.join(__dirname, "./**/*.resolvers.*"));
const permissionsArray = loadFilesSync(path.join(__dirname, "./**/*.permissions.*"));

export const resolvers: Resolvers = mergeResolvers(resolversArray);
export const permissions = mergeResolvers(permissionsArray);
```
{% endcode %}

create graphql/schema/types folder to keep all the graphql types and resolver files

{% code title="graphql/data-sources/blob/blob-data-source.ts" %}
```
import { DataSource,DataSourceConfig } from 'apollo-datasource';
import { Context as GraphQLContext } from '../../context';
import { Passport } from '../../../domain/contexts/iam/passport';
import { BlobStorage } from '../../../infrastructure/services/blob-storage';

export class BlobDataSource<Context extends GraphQLContext> extends DataSource<Context> {
  private _context: Context;
  private _blobStorage: BlobStorage;
  
  public get context(): Context { return this._context;}

  public async withStorage(func:(passport:Passport, blobStorage:BlobStorage) => Promise<void>): Promise<void> {
    let passport =  this.context.passport; //await getPassport(this.context);
    await func(passport, this._blobStorage);
  }

  public initialize(config: DataSourceConfig<Context>): void {
    this._context = config.context;  
    this._blobStorage = new BlobStorage();  
  }  
}
```
{% endcode %}

{% code title="graphql/data-sources/blob/index.ts" %}
```
import { Communities } from "./communities";
import { Members } from "./members";
import { Properties } from "./properties";

export const Blob  = {
  communityBlobAPI: new Communities(),
  memberBlobAPI: new Members(),
  propertyBlobAPI: new Properties(),
}
```
{% endcode %}
