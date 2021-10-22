# Azure Data Factory

In the Azure Portal - search for Data Factories  and select it - it will take yoou to the [Data Factories blade](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DataFactory%2FdataFactories).

Click **Create data factory** button, you will be display the Create Data Factory  blade,&#x20;

Subscription: <\<your subscription>>\
Resource Group: sharethrift-rg\
Region: east US 2\
Name: sharethrift-data-factory\
Version: v2\
\
Click Next: Git Configuration\
Check "Configure Git Later"

Click: Review + create (wait for the validationo to complete), then click "Create", you will be taken to the overview page, click the "Go to resource" button\


On the data factory instance page,Click the **author and monitor** button, the Azure Data Factory portal will open.

Click Create Pipeline:

in the properties pane on the right of the screen:

* Name: account-sync
* Description: Copies accounts from legacy database to cloud database.
* Concurrency: <\<leave empty>>

Under Activities, choose Move & transform,  drag Copy data to the Design Surface

At bottom of screen:

* **General** Tab
  * Name: copy-accounts
  * (leave other settings the default)
* **Source** Tab:
  * Source dataset:
    * Click **+ New** (chooser pane appears)
      * Select appropriate Databases Connection type (e.g. **PostgreSQL**)
    * Choose **Open** next to the source connection that you selected
      * Linked service:&#x20;
        * Click **+ New** ( connection pane shows) _or choose an existing connection _
          * Name: **accountsdb**
          * (other appropriate details.. may need to set "not validate sever certificate")
        * Select the appropriate table and click preview data to ensure data is correct
        * Select **"account-sync"** tab at top to return to the pipeline
  * &#x20;Use Query: **table**
* **Sink** Tab:
  * Sink dataset:
    * Click **+ New** (chooser pane appears0
      * Select **Azure CosmosDB (MongoDB API)**
    * Choose Open next to the sink dataset that you selected
      * Linked service:
        * Click **+ New **(connection pane shows)
        * Name: **sharetriftdb**
        * (other appropriate details..)
        * Account selection method : **From Azure subscription**
          * Subscription: **<\<your subscription>> **
          * Account: **sharethrift**
          * DBName: **sharethrift**
      * Collection Name: **accounts**
      * Select **"account-sync"** tab at top to return to the pipeline
  * Write behavior: **upsert**
  * Write batch size: **1000**
* **Mapping** Tab:
  * Click **Import Schemas**
  * Click **New Mapping**
    * Select source / destination columns as appropriate
* Click **Validate Pipeline** at top of screen
* Choose **Debug** - at top of screen



Cosmos:

Firewall tab -> all networks









:
