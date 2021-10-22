---
description: (~5 minutes)
---

# Function App Settings

### Azure Configuration

* Navigate to the East US2 ShareThrift function app (data-access)
* In the left menu, under settings, choose configuration
  * Create the following App Settings:
    * COSMOSDB\_HOST: (from CosmosDB > Settings > Connection String > HOST (e.g.: sharethrift.mongo.cosmos.azure.com)
    * COSMOSDB\_PORT: (from CosmosDB > Settings > Connection String > PORT (e.g.: 10255)
    * COSMOSDB\_USER : (from CosmosDB > Settings > Connection String > USERNAME (e.g. sharethrift)
    * COSMOSDB\_DBNAME: sharethrift
    * COSMOSDB\_POOL\_SIZE: 10
    * SENDGRID\_EMAIL\_FROM: <\<the verified email you set up with sendgrid>>
  * Choose **Save**
  * Choose **Continue**, wait for the save to complete.
  * Next create Key Vault secured App Settings:
    * First go to Key Vault and get the access string for each of the two secrets created earlier:
    * KeyVault > sharethrift > secrets > COSMOSDB-PASSWORD
      * Copy the Secret Identifier, paste into excel
    * KeyVault > sharethrift > secrets > SENDGRID-API-KEY
      * Copy the Secret Identifier, paste into excel
    * Create a new concatenated string by wrapping following around each secret
      * **@Microsoft.KeyVault(SecretUri=**
      * _<\<Secret Identifer>>_
      * **)**
    * Use the concatenated strings for the App Settings of:
      * COSMOSDB\_PASSWORD
      * SENDGRID\_API\_KEY
    * Choose Save
* Repeat the Same for the US West 2 Function data-access-west

### Local Configuration

Update the local settings as well:

{% code title="/azure-quickstart/data-access/local.settings.json" %}
```
{
  ...
  "Values": [
    {
    ...
		"COSMOSDB_DBNAME": "sharethrift",
		"COSMOSDB_HOST": "<<SAME AS FROM ABOVE>>",
		"COSMOSDB_PORT": 10255,
    "COSMOSDB_POOL_SIZE" : 10, 
    "COSMODDB_USER": "<<SAME AS FROM ABOVE>>",
		"COSMOSDB_PASSWORD": "<<SAME AS FROM ABOVE>>",
    
    "SENDGRID_API_KEY": "<<SAME AS FROM ABOVE>>"
    "SENDGRID_EMAIL_FROM": "<<SAME AS FROM ABOVE>>"   
    ...
    }
  ]
  ...
}
```
{% endcode %}



