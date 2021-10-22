---
description: (~17 minutes)
---

# Function



### US East 2 Function

* Navigate to the [Create Function App](https://portal.azure.com/#create/Microsoft.FunctionApp) resource in the Azure Portal
* Select the appropriate Subscription and the **rg-sharethrift** resource group
* Instance Details
  * Function App Name: **data-access-<\<random-number>> **
    * _\*\* when creating for west 2 use **data-access-west<\<random-number>> **_
  * Publish: **code**
  * Runtime Stack: **Node.js**
  * Version: **12 LTS**
  * Region: **East US 2**
    * _\*\* when creating for west 2 use **West US 2**_
  * Choose **Next : Hosting > **
* Hosting Details:
  * Storage Account: (select the **sharethrift\<random number> **storage account created earlier)
    * _\*\* when creating for west 2 select the _**sharethriftwest\<random number> **account
  * Operating System: **linux**
  * Plan type: **Consumption (Serverless) **
  * Choose Next: **Monitoring > **
* Application Insights:
  * Enable Application Insights: **Yes**
  * Application Insights: **sharethrift (East US 2)**
    * _\*\* when creating for west 2 select **sharethriftwest (West US 2)**_
  * Choose **Review + Create**
  * Choose **Create** (wait for it to complete)
  * Choose **Go to resource**
* Add App Settings for improved performance
  * In the function app just created navigate to Configuration&#x20;
  * Choose **+ New Application Setting** for each of the following
    * Improve Cold Start performance ([details](https://docs.microsoft.com/en-us/azure/azure-functions/run-functions-from-deployment-package))
      * Name: **WEBSITE\_RUN\_FROM\_PACKAGE**
      * Value: **1**
    * Improve Node performance ([details](https://docs.microsoft.com/en-us/azure/azure-functions/functions-app-settings#functions\_worker\_process\_count))
      * Name: **FUNCTIONS\_WORKER\_PROCESS\_COUNT**
      * Value: **10**
  * Choose** Save**
  * Choose** Continue **(wait until completion)
* Add CORS headers
  * In the function app just created navigate to CORS
  * Check the Enable Access-Control-Allow-Credentials
  * Add the URL of the website created in the CDN step&#x20;
    * e.g. https://sharethrift\<random number>.azureedge.net/
* Add System Assigned Identity
  * In the function app just created navigate to Settings > Identity
  * Under System Assigned, Status : Select On
  * Choose Save - a dialog confirming your choice will appear, click Yes, wait for it complete.
  * Click the Azure Role Assignments button:
    * You will be taken to the Azure Role Assignments Blade
    * Click Add role assignment (preview)
    * In the Add Role Assignment blade
      * Scope: Key Vault
      * Subscription: (choose the appropriate subscription)
      * Resource: sharethrift
      * Role: Key Vault Secrets User (preview)

### US West 2 Function

Repeat the same process above, but instead make the following changes

* Name the function:
  * &#x20;**data-access-west<\<random-number>> **
* Select the Region:
  * **US West 2**
* Select the storage account:
  * **sharethriftwest\<random number> **storage account created earlier
* Select the application insights:
  * _**sharethriftwest (West US 2)**_

_**Keyvault Settings**_

* Need to add Get permissions on secret for the 2 identities you just created

Resources

* Microsoft
  * [Functions Reference for NodeJS](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#scaling-and-concurrency)
