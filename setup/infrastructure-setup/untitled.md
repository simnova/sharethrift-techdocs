---
description: (~20 minutes)
---

# CosmosDB

## Why CosmosDB + MongoDB

CosmosDB is a good solution for building a solution for a startup, it allows solutions to start small and to scale globally, all with little management overhead. There are, of course drawbacks as well, the major one being that cost can be come an issue. This project will attempt to keep costs in check by fronting access to CosmosDB with Apollo GraphQL which if architected appropriately can make server side calls more efficient and can leverage caching.&#x20;

The NodeJS community has excellent support for MongoDB and the maturity in working with unstructured data provides a good paring with Apollo GraphQL and helps to meet the demands of modern consumer created data driven applications.

### Set up an instance of CosmosDB

* Navigate to the [Create Azure CosmosDB Account](https://portal.azure.com/#create/Microsoft.DocumentDB)  in the Azure portal
* Select the appropriate Subscription
* Select the rg-sharethrift resource group
* Instance Details:
  * Account Name: **sharethrift<\<Random Number>>**
  * API: **Azure CosmosDB for MogodDB API**
  * Notebooks (Preview): **On**
  * Location: **(US) East US 2**
  * Apply Free Tier Discount: **Apply**
  * Account Type: **Production**
  * Version: **4.2**
  * Multi-region Writes: **Enable (may want to Disable for lower cost)**
  * Availability Zones: **Enable**
  * Click Next: **Networking**
* Networking
  * Network Connectivity - Connectivity method: **All networks **~~**Public endpoint (selected networks)**~~
  * ~~Configure Firewall~~
    * ~~Allow access from Azure Portal: **Allow**~~
    * ~~Allow access from my IP: **Allow**~~
  * Click **Review + create**
  * Click **Create** (wait for completion)
* Click Data Explorer
  * Choose New Database (Pane will show)
    * Database ID: **sharethrift**
    * Provision throughput: **Checked**
      * **Important:** This allows each collection to share in the 1,000 RU allotment for the free tier.
    * Max R/Us: (leave default 1,000 )
    * Click OK

#### Recommended Reading

* Microsoft
  * [Plan and manage costs for Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/plan-manage-costs)
* MongoDB
  * [The MongoDB 3.6 Manual](https://docs.mongodb.com/v3.6/)&#x20;
    * (3.6 is the max version supported by Cosmos at the time of writing)

#### Recommended Tools

* [3T](https://robomongo.org) - Robo3T(free) / Studio3T(commercial)
  * How to [Use Studio3T to connect to Cosmos](https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-mongochef)
  * How to [Use Robo3T to connect to Cosmos](https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-robomongo)
