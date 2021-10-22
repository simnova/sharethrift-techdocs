# Apollo Connection



In **terminal** go to `azure-quickstart/data-access` and issue the following command:

```bash
npm install mongoose
```

in `azure-quickstart/data-access` create the following directory structure:

`shared-code/data-sources/cosmos-db/`

Add the a connection file for connecting to CosmosDB (MongoDB):

{% code title="azure-quickstart/data-access/shared-code/data-sources/cosmos-db/connect.ts" %}
```typescript
import mongoose from 'mongoose';

async function connect() {
    try {
        await mongoose.connect("mongodb://"+process.env.COSMOSDB_HOST+":"+process.env.COSMOSDB_PORT+"/"+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb&retryWrites=false", {
            auth: {
                user: process.env.COSMODDB_USER,
                password: process.env.COSMOSDB_PASSWORD
            },
            tlsInsecure:   process.env.NODE_ENV === "development", //only true for local developent - required for Azure Cosmos DB emulator
            useNewUrlParser: true, 
            useUnifiedTopology: true,
            useFindAndModify: false,
            poolSize: Number(process.env.COSMOSDB_POOL_SIZE)
        }).then(() => console.log(`🗄️ Successfully connected Mongoose to ${mongoose.connection.name} 🗄️`));
    } catch (error) {
        console.log(`🔥 An error ocurred when trying to connect Mongoose with ${mongoose.connection.name} 🔥`)
        throw error;
    }
}

export default connect;
```
{% endcode %}

Add an import and start the cosmos connection in the main Apollo Function:

{% code title="azure-quickstart/data-access/shared-code/data-sources/cosmos-db/connect.ts" %}
```typescript
.../*<<other import statements>>*/...
import cosmosdbconnect from '../sharedCode/datasources/cosmosDb/connect';


/* IIFE for connecting to CosmosDB */
(async () => {
  try {
    await cosmosdbconnect();
  } catch (cosmosDbConnectError) {
    appInsightsClient.trackEvent({name: "COSMOSDB CONNECTION", properties: {customProperty: "Cannot Connect to Cosmos", details: cosmosDbConnectError}});
    appInsightsClient.trackException(cosmosDbConnectError)
  }
})();

.../*<<ApploServer startup>> */..
```
{% endcode %}

Add the following the `localSettings.json` file, be sure to replace the values in <<>> with values from your Cosmos DB settings.

{% code title="azure-quickstart/data-access/localSettings.json" %}
```typescript
{
  ...
  "Values": {
    ...
    "COSMODDB_USER": "sharethrift",
		"COSMOSDB_DBNAME": "sharethrift",
		"COSMOSDB_HOST": "sharethrift<<RANDOM NUMBER>>.mongo.cosmos.azure.com",
		"COSMOSDB_PASSWORD": "<<YOUR PASSWORD>>",
		"COSMOSDB_PORT": 10255,
		"COSMOSDB_POOL_SIZE" : 10,
    ...
  }
  ...
}
```
{% endcode %}

