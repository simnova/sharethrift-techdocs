---
description: (~8 minutes)
---

# Create AppInsight Account

### Create Log Analytics Workspace

In this project, logs will be stored using the centralized model, meaning all data will be collected in a single region (US) East 2 - however one could choose other models as discussed in [Microsoft's documentation](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/design-logs-deployment).

* Navigate to the [Create Log Analytics](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS) workspace in the Azure Portal
  * Select the appropriate Subscription and the rg-sharethrift resource group.
  * Instance Details:
    * Name: **sharethrift<\<random number>>**
    * Region: **(US) East 2**
  * Click **Review+Create**
  * Click **Create** (wait for it to complete)
  * Click **Go to Resource** once completed

#### Configure limits on Log Analytics to limit charges

* In the Log Analytics workspace just created, navigate to the **Usage and Estimated Costs** in the left pane
* Choose **Daily Cap** - and turn to **ON**,&#x20;
  * Set the limit to **0.5** GB Day
  * Click **OK**
* Choose **Data Retention** - and ensure it is set to **30 days** (should be by default)

### Crate App Insights Resource

#### US East 2

* Navigate to the [Create App Insights](https://portal.azure.com/#create/Microsoft.AppInsights) resource in the Azure Portal
* Select the appropriate Subscription and the rg-sharethrift resource group.
* Instance Details:
  * Name: **sharethrift**
  * Region: **(US) East 2**
  * Resource Mode: **Workspace-based**&#x20;
* Workspace Details:
  * Select the appropriate Subscription
  * Log analytics workspace: **sharethrift \[eastus2]**
* Click **Review+Create**
* Click **Create** (wait for it to complete)

#### US West 2

* Navigate to the [Create App Insights](https://portal.azure.com/#create/Microsoft.AppInsights) resource in the Azure Portal
* Select the appropriate Subscription and the rg-sharethrift resource group.
* Instance Details:
  * Name: **sharethriftwest**
  * Region: **(US) West 2**
  * Resource Mode: **Workspace-based**&#x20;
* Workspace Details:
  * Select the appropriate Subscription
  * Log analytics workspace: **sharethrift \[eastus2]**
* Click **Review+Create**
* Click **Create** (wait for it to complete)
