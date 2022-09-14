---
description: (~10 minutes)
---

# Front Door

Azure Front Door provides SSL Termination / Traffic Routing / Web Application firewall and many other features.

Create a Web Application Firewall (WAF) Policy

* In the Azure portal select [Create a WAF Policy](https://portal.azure.com/#create/Microsoft.WafPolicy)
* Policy For : Global WAF (Front Door)
* Choose the appropriate subscription and the rg-sharethrift resource group (the resource group location will be prefilled in)
* Instance details
* Policy Name: sharethrift
* Policy State: enabled
* Choose Next : Policy Settings  >&#x20;
* Mode : Prevention
* Choose Next: Managed Rules >
* Ensure  **DefaultRuleset** is checked.
* Click Review + Create
*   Click Create





Create the Front Door

* In the Azure portal select [Create a Front Door](https://portal.azure.com/#create/Microsoft.Frontdoor)
* Choose the appropriate subscription and the rg-sharethrift resource group (the resource group location will be prefilled in)
* Choose Next : Configuration >
* In the configuration screen in **Frontends/domains**, click +
  * Add a frontend host will be displayed
  * Enter Host Name: sharethrift<\<ranndom number>>
  * Leave Session Affinity as **Disabled**
  * Set Web Application Firewall to **Enabled**
  * Policy: select **sharethrift**
  * Click **Add**
* In the configuration screen in **Backend Pools, click +**
  * For name: set to **sharethrift-data-access**
  * In Backends, click **+ add a backend**
    * Add the data-access in East US 2
      * Host type : App Service
      * Subscription : <\<make the appropriate choice>>
      * Backend Host Name: data-access-<\<random number>>.azurewebsites.net
      * (leave the rest of the options the way they are)
      * Click Add
    * Add the data-access in West US 2
      * Host type : App Service
      * Subscription : <\<make the appropriate choice>>
      * Backend Host Name: data-access-west<\<random number>>.azurewebsites.net
      * (leave the rest of the options the way they are)
      * Click Add
  * &#x20;in Health Probes specify&#x20;
    * Path: **/api/graphql/.well-known/apollo/server-health**
    * Probe Method: **GET**
  * (leave the rest of the options the way they are)
  * Click **Add**
* In the configuration screen in **Routing Rules, click +**
  * Name: **sharethrift-data-access**
  * Accepted Protocol: **HTTPS only**
  * **Patterns to match : (leave as is)**
  * **Route Type: Forward**
  * **Backend Pool: sharethrift-data-access**
  * **Forwarding Protocol: Https Only**
  * **URL Rewrite: Disabled**
  * **Caching: Enabled**
  * Query String Caching Behavior: **Cache Every Unique URL**
  * Dynamic Compression: **Enabled**
  * Use default cache duration: **Yes**
  * **Click: Add**
* **Click Review+Create**
* **Click Create (wait for completion)**

Resources

* Microsoft
  * [Create a FrontDoor - Portal](https://docs.microsoft.com/en-us/azure/frontdoor/quickstart-create-front-door)
  * [Apollo GraphQL Healthcheck](https://www.apollographql.com/docs/apollo-server/monitoring/health-checks/)

\
&#x20;
