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
npm i moment dayjs
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
