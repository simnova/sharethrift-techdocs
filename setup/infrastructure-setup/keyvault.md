---
description: (~7 minutes)
---

# KeyVault

* Navigate to the [Create Key Vault](https://portal.azure.com/#create/Microsoft.KeyVault) resource in the Azure portal.
* Select the appropriate subscription and choose the **rg-sharethrift** resource group.&#x20;
* Instance Details:
  * Key vault name: **sharethrift-keyvault\<random number if needed>**
  * Region: **East US 2**
  * Pricing Tier: **Standard**
  * Choose : **Next > Access Policy**&#x20;
* Access Policy
  * Permission Model : **Azure role-based access control**&#x20;
  * Choose : **Next > Networking**
* Networking
  * Connectivity Method: **All networks**
    * **TODO: See how selected networks can work**
      * Allow trusted Microsoft services to bypass this firewall? : **Yes**
  * Choose : **Review + Create**
  * Choose : **Create** (wait for completion)
  * Click **Go to Resource**
*   In the newly created Key Vault, navigate to the **Access Control (IAM)** menu item

    * Choose **+ Add**  then **Add role assignment**
    * Role: **Key Vault Administrator**
    * Assign Access To: **User, group, or service principal**
    * Select:  **(choose your account from the list below, it may look a little weird)**
    * Click **Save**

    ****
* (SKIP) In the newly created Keyvault, navigate to the Networking menu item
  * Under Firewall, add your current public IP Address
  * Click Save
* In the newly created Keyvault, navigate to the **Secrets** menu item
  * Add COSMOSDB Password
    * Choose **Generate/Import**
    * Upload Options: **Manual**
    * Name: **COSMOSDB-PASSWORD**
    * Value: **<\<enter the password from CosmosDB Primary Password>>**
    * Leave Content Type _empty_
    * Leave Set Activation Date / Set Expiration Date unchecked
    * Ensure Enabled: **Yes**
    * Click **Create**
  * Add SendGrid API Key
    * Choose **Generate/Import**
    * Upload Options: **Manual**
    * Name: **SENDGRID-API-KEY**
    * Value: **<\<enter the SendGrid API key you created earlier>>**
    * Leave Content Type _empty_
    * Leave Set Activation Date / Set Expiration Date _unchecked_
    * Ensure Enabled: **Yes**
    * Click **Create**

Key Vault is already GeoRedundant therefore it is not necessary to create a secondary Key Vault in another region \[[details](https://docs.microsoft.com/en-us/azure/key-vault/general/disaster-recovery-guidance)]
