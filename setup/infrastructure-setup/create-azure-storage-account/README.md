---
description: (25 minutes)
---

# Storage Account & CDN

## Why Azure Storage for Static Websites

For web applications which are entirely static (html/css/javascript) which leverage a separate stateless system for any dynamic data needs, hosting a website in from Azure Storage is an extremely cost effective solution.

In order to the host the website we'll create a storage account, configure it to host a website and set up a CDN, the CDN helps in delivering content quickly to visitors of the site.

Azure functions also rely on Azure Storage, one of these be used for the website as well as supporting the Azure function. &#x20;

### Create the Storage Account

**Storage Account for Azure Function (US WEST2)**

* Navigate to [Create Storage Account](https://portal.azure.com/#create/Microsoft.StorageAccount) in the Azure Portal
  * Select the subscription and the **rg-sharethrift** Resource Group created earlier.
  * Instance details:
    * Storage account name: _**sharethriftwest<\<some random number>>**_
      * _Be sure to replace <\<some-random-number>> with a value, e.g. sharethriftwest123. Since storage account names need to be globally unique, you may need to try a few times before getting an unused number._&#x20;
    * Location: **(US) West US 2**&#x20;
    * Performance: **Standard**
    * Account kind: **StorageV2** (**general purpose V2)**
    * Replication: **Read-access geo-zone redundant storage RA-GRS**
    * Click **Next : Networking**
  * Network connectivity
    * Connectivity method: **Private Endpoint**
    * Private Endpoint: \<don't add anything at this time>
    * Network Routing: **Microsoft network routing (default)**
  * Click **Review + Create**
    * Click: **Create** (wait for it to be created)

#### Storage Account for Static Website and Azure Function (US EAST2)

* Navigate to [Create Storage Account](https://portal.azure.com/#create/Microsoft.StorageAccount) in the Azure Portal
  * Select the subscription and the **rg-sharethrift** Resource Group created earlier.
  * Instance details:
    * Storage account name: _**sharethrift<\<some random number>>**_
      * _Be sure to replace <\<some-random-number>> with a value, e.g. sharethrift123. Since storage account names need to be globally unique, you may need to try a few times before getting an unused number._&#x20;
    * Location: **(US) East US 2**
    * Performance: **Standard**
    * Account kind: **General Purpose V2**
    * Replication: **Read-access geo-zone redundant storage RA-GZRS**
    * Click **Next : Networking**
  * Network connectivity
    * Connectivity method: **Public (all networks)**
    * Private Endpoint: \<don't add anything at this time>
    * Network Routing: **Microsoft network routing (default)**
  * Click **Review + Create**
    * Click: **Create** (wait for it to be created)
    * Click: **Go to Resource**&#x20;

### **Enable Static Website**

* In the _**US East 2**_ storage account you just created, under select **Static website** in the navigation menu to the left under Settings
* Select **Enabled**
* Index document name:  **index.html**
* Error document path: **index.html**
* Click Save

### Add a CDN to the Static Website

* In the storage account you just created, select **Azure CDN** in the navigation menu to the left.
* CDN profile : **Create New**
* CDN profile name: **sharethrift**
* Pricing tier: **Standard Microsoft**
* CDN endpoint name:
  * _**sharethrift-<\<some random number>>(static website)**_
* Origin Hostname (select the option with (**static website**) from the dropdown):&#x20;
* _Click **Create (**wait)_

### Create Multi-Origin CDN

* While still in the storage account you created, select  Settings > Geo-replication in the navigation menu on the left
  * Under Storage Endpoints click **View All**
  * Note the **Secondary static website endpoint** (copy to a text document)
* Navigate to the CDN just created
* **Create Origin Group**
  * Under Settings > Origin - click **+ Create Origin Group**, Add Origin Group panel shows
    * Origin group name: **sharethrift-blobgroup**
    * Probe status: **Enabled**
    * Probe path: **/**
    * Probe interval: **4 minute**
    * Probe protocol: **HTTPS**
    * Probe method: **Get**
    * Default origin group: **(checked)**
    * Click **Add** _(wait)_
* **Add Second Origin**
  * Still within Settings > Origin - click **+ Create Origin** (the Add Origin panel shows)
    * Name: **sharethrift-secondary-blob-core-windows-net**
    * Origin type: **Custom origin**
    * Origin hostname: **<\<endpoint copied from earlier **_**without**_** the https:// and slash at end>>**
    * Origin host header: **<\<same as origin hostname e.g.: sharethrift-secondary.z20.web.core.windows.net >>**
    * HTTP port: **80**
    * HTTPS port: **443**
    * Priority: **1**
    * Weight: **1000**
    * Enabled: **(checked)**
    * Click **Add** _(wait)_
* **Assign Second Origin to Origin Group**
  * Select the **sharethrift-blobgroup** you created earlier (Update Origin Group pane shows)
  * Under Origins click **+ Select Origin**, a _Select and origin to add_ dialog appears
    * Choose the second origin you just created from the dropdown and click **OK**
  * Click **Save** (wait)

(Optional) Add Custom Domain to the CDN

* Navigate to the CDN just created, choose **Custom Domains** under Settings&#x20;
  * Note the **Endpoint hostname** (e.g. **sharetrift.azureedge.net**), you'll use this in your DNS Settings
  * Log into your DNS Registrar's DNS settings, select your domain name (e.g. sharethrift.com)
    * Add two CNAME Records
      * Verification Record
        * CNAME: **cdnverify**
        * Points to: **cdnverify.<\<endpoint hostname>>**
      * WWW Record
        * CNAME: **www**
        * Points to: **<\<endpoint hostname>>**
  * Custom hostname: **<\<your domain name e.g.: www.sharethrift.com>>**
  * Click **Add**
* Add HTTPS to the domain
  * Select the custom domain you just created
  * Custom domain HTTPS: On
  * Certificate management type: CDN managed
  * Minimum TLS version: TLS 1.2



Resources:

* Microsoft -&#x20;
  * [Multi-origin CDN](https://docs.microsoft.com/en-us/azure/cdn/endpoint-multiorigin) (no longer in preview as of Jan 2021, available for production workloads)
    * This couples well with the RA-GZRS blob website which is available in multiple regions.

## Resources

* Microsoft
  * [Create a general-purpose storage account](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json\&tabs=azure-portal)
  * [Host a static website in Azure Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website-how-to?tabs=azure-portal)
  * [Integrate a static website with Azure CDN](https://docs.microsoft.com/en-us/azure/storage/blobs/static-website-content-delivery-network)

