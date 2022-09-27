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
