# Connect Apollo

dataacess





```bash
/data-access/
- /graphql/ 
- /shared-code/
  - /datasources/ 
    - /cosmosdb/
      - connect.ts    #connects to database
      - /models/      #defines datatypes in db  
      - /functions/   #common db functions   
```

Install mongoose - a library for working with MongoDB in node

```bash
npm i mongoose
```

{% code title="/data-access/shared-code/datasources/cosmosdb/connect.ts" %}
```bash
import mongoose from 'mongoose';

async function connect() {
    if(!process.env.COSMOSDB || process.env.COSMOSDB.length === 0) throw new Error("CosmosDB connection string not found.");
    if(!process.env.COSMOSDB_DBNAME || process.env.COSMOSDB_DBNAME.length === 0) throw new Error("CosmosDB name not found.");
    try {
        await mongoose.connect(`${process.env.COSMOSDB}&retrywrites=false`, {
            useNewUrlParser: true, 
            useUnifiedTopology: true,
            useFindAndModify: false,
            tlsInsecure: process.env.NODE_ENV === "development", //only true for local developent - required for Azure Cosmos DB emulator
            dbName: process.env.COSMOSDB_DBNAME,
            poolSize: Number(process.env.COSMOSDB_POOL_SIZE)
        }).then(() => console.log(`🗄️ Successfully connected Mongoose to ${mongoose.connection.name} 🗄️`));

        if(process.env.NODE_ENV === "development"){
            mongoose.set('debug', true);
            mongoose.set('debug', {shell: true});
        }
    } catch (error) {
        console.log(`🔥 An error ocurred when trying to connect Mongoose with ${mongoose.connection.name} 🔥`)
        throw error;
    }
}

export default connect;
```
{% endcode %}

{% code title="local.settings.json" %}
```bash
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "COSMOSDB" : "mongodb://xxxx/admin?ssl=true",
    "COSMOSDB_DBNAME" : "sharethrift",
    "COSMOSDB_POOL_SIZE": "10",
    "NODE_ENV": "development"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  }
}

```
{% endcode %}

Azure Function Settings:

* COSMOSDB
* COSMOSDB\_DBNAME
* COSMOSDB\_POOL\_SIZE
* NODE\_ENV
