---
description: (~5-10 minutes)
---

# DevOps

Navigate to the Azure DevOps Signup website.

* Sign up for a free Azure DevOps account ([instructions](https://docs.microsoft.com/en-us/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops))
* Create a new _private_ project named **ShareThrift** ([instructions](https://docs.microsoft.com/en-us/azure/devops/organizations/projects/create-project?view=azure-devops\&tabs=preview-page))&#x20;
*



Give Azure DevOps permissions to Azure Storage



* In Azure DevOps, Go to Project Settings > Service Connections >  Manage Service Connection Roles
  * Select the service connection named after your Subscription _or_ create a new one using **Azure Resource Manager** as the connection type with the Authentication method of **Service Principal (automatic)** and Scope Level of **Subscription** and Subscription name created in Azure Portal with resource group created in **rg-sharethrift** Service Connection Name of **ShareThrift**&#x20;
  * Click Manage Service Principal, a new browser tab to the Azure Portal will open
  * Note the Display Name
* Navigate the the sharethrift storage Account
* Choose Access Control (IAM)
* Choose + Add > Add role assignment, the Add Role Assignment blade will show
  * Role: Storage Blob Data Contributor
  * Assign access to: User, group or service principal
  * Select: (choose the Display Name of the Service Principal from above)
  * Click Save

