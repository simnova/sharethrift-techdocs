---
description: How Azure Maps was implemented into Owner-Community
---

# Azure Maps

npm package installed:

* @azure/arm-maps

### Azure Maps

A collection of geospatial services provided by Microsoft Azure. Used in Owner Community for address search completion.&#x20;



### Search Completion Implementation

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

### Getting the SAS Token

A user can request the auth token by using the VS Code Azure extension or through the Azure CLI by running az login in your command line. You may have to install the Azure CLI ([https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli)).&#x20;

Then the object AzureMapsManagementClient has an interface Accounts that has a method listKeys(), which would provide the SAS Token.&#x20;

More information about AzureMapsManagementClient can be found here: [https://docs.microsoft.com/en-us/javascript/api/@azure/arm-maps/?view=azure-node-preview](https://docs.microsoft.com/en-us/javascript/api/@azure/arm-maps/?view=azure-node-preview)

With the SAS Token, you can now make requests to the Azure Maps Fuzzy Search API.

More about the API can be found here: [https://docs.microsoft.com/en-us/rest/api/maps/search/get-search-fuzzy?tabs=HTTP](https://docs.microsoft.com/en-us/rest/api/maps/search/get-search-fuzzy?tabs=HTTP)

### Autocomplete Functionality

Autocomplete was implemented with Ant Design's autocomplete component.&#x20;

When there is a change in the search bar, it will requery the API and generate new results as the user types.

Example:

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

For reference, owner-community can be found here:

{% embed url="https://github.com/simnova/ownercommunity-ui" %}

{% embed url="https://github.com/simnova/ownercommuntiy-data-access" %}
