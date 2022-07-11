# Create AAD B2C Instance



## Create a Tenant

IMPORTANT: Before starting - Add the _Microsoft.AzureActiveDirectory_ resource provider to your subscription by following[ these instructions](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/error-register-resource-provider#solution-3---azure-portal) from Microsoft.

Navigate to your [AAD Directory](https://portal.azure.com/#blade/Microsoft\_AAD\_IAM/ActiveDirectoryMenuBlade/Overview) and click **Create a tenant** at the top of the screen.

The **Create a tenant screen** will be shown.

Select the **Azure Active Directory (B2C)** as your choice. - Click Next

In the Configuration Section choose:

* Directory Details
  * Organization name \* : **Sharethrift B2C**
  * Initial domain name \* : **sharethriftb2c<\<some random number>>**
  * Country/Region: **United States**
* Subscription
  * Subscription: **<\<your subscription>>**
  * Resource Group: **rg-sharethrift**

Click Review + Create --> Create

Switch to the new Directory by clicking your name in the upper right corner of the screen and select _Sharethift B2C_ - if  you don't see it, reload the webpage.



### App Registration for Partner Portal



Choose **App registrations** in the menu on the left under the Manage category.

Choose **New registration**

Register an application screen will show.

* Name: **Sharethrift Partner Portal** \
  **external verification**
*   Supported account types: **Accounts in this organizational directory only (Sharethrift B2C Dev only - Single tenant)**

    <mark style="color:red;">Note:</mark> If you want to test Custom Policies in Identity Experience Framework, you must choose:  **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**
* Redirect URI:
  * Type Dropdown: **Single-page application (SPA)**
  * URL: **http://localhost:4000**
* Permissions: **(leave default values set as-is)**
* Click **Register**&#x20;

### App Registration for Programmatic Account Management

Choose App Registrations in the menu on the left.

Choose New registration

Register an application screen will show.

* Name: **Sharethrift Account Management**
* Supported account types: **Accounts in this organizational directory  only (Sharethrift B2C only - Single Tenant)**
* Redirect URI:
  * Type Dropdown: **Public client/native (mobile & desktop)**
  * URL: http://localhost:4000
* Permissions: **(leave default values set as-is)**
* Click Register (navigate to new app)

#### Add Key to App Registration

Select **Certificates & secrets** in menu to left under the Manage category

Under _Client secrets_ choose **New client secret** (a dialog will appear):

* Description: **programmatic-account-management**
* Expires: **Custom**
* Start: **\<today>**
* End: **\<max expiration date shown>**

Once created you will see "**Value**" copy this someplace safe, _you will not be able to reference this value after seeing it at this time._



#### Add API Permission

Select **API permissions** under manage on menu on left under Manage

Click + Add a permission  (the Request API permissions blade shows)

Select Microsoft APIs > Microsoft Graph

Choose Application Permissions

Type User.ReadWrite.All. Check User.ReadWrite.All from the User dropdown

Click Add Permissions at bottom of screen.

Click Grant Admin consent for Sharethrift B2C



#### Grant Application Ability to manage accounts

Navigate to [AAD B2C Roles and Administrators](https://portal.azure.com/#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementMenuBlade/manageRoles), search for "User administrator" and click on it select it.

The Assignments for User administrator is displayed.

Click Add assignments (the add assignments blade will show)

Search for **Sharethrift Account Management**, select it and click **Add**

****

### **App Registration for IdentityExperienceFramework**

In menu on left under **Manage** choose **App registrations**

Click **New registration** at the top of the screen

* Name: **IdentityExperienceFramework**
* Supported account types: **Accounts in this organizational directory  only (Sharethrift B2C only - Single Tenant)**
* Redirect URI:
  * Type Dropdown: **Web**
  * URL: **https://<\<your-tenant-name>>.b2clogin.com/<\<your-tenant-name>>.onmicrosoft.com** (e.g.&#x20;
    * https://sharethriftb2c.b2clogin.com/sharthriftb2c.onmicrosoft.com
* Click Register (you'll automatically navigate to new app)

In menu on left under **Manage**, select **Expose an API**

Add a scope (accept the default random name), Save and continue

* Click **Add a scope**
* Scope name: **user\_impersonation**&#x20;
* Admin consent display name: **Access IdentityExperienceFramework**&#x20;
* Admin consent description: **Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.**
* State: **Enabled**
* Click **Add Scope:**

**Navigate back to the App Registrations under the Experience Framework by clicking on "identity experience framework" at the top of the screen**&#x20;

****

### **App Registration for ProxyIdentityExperienceFramework**

In menu on left under **Manage** choose **App registrations**

Click **New registration** at the top of the screen

* Name: **ProxyIdentityExperienceFramework**
* Supported account types: **Accounts in this organizational directory  only (Sharethrift B2C only - Single Tenant)**
* Redirect URI:
  * Type Dropdown: **Public client/native (mobile & desktop)**
  * URL: **myapp://auth**
* **Permissions: (leave values as their defaults)**
* Click Register (you'll automatically navigate to new app)

In menu on left, under Manage, choose Authentication

In Advanced Settings -> Allow Public Flows - Choose **Yes**&#x20;

Click **Save** at top of screen



In menu on left, under **Manage** choose **API Permissions**

Click **Add a permission,** the **Request API permissions** blade shows:

Select an API: **My APIs**

Choose **IdentityExperienceFramework**

Choose **: Delegated permissions**

Select Permissions: **check user\_impersonation**

Click **Add Permissions**

Click **Grant admin consent for Sharethrift B2C**, a dialog will pop asking you to confirm, click **Yes**







### Identity Experience Framework

Navigate to [Azure AD B2C](https://portal.azure.com/#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementMenuBlade/overview)**,** in the menu one the left under the Polices section choose Identity Experience Framework.



The Identity Experience Framework blade will be shown.



#### Create Policy keys

You'll see B2C\_1A\_ automatically prefixed to whatever you add, don't freak out, it's all good.

Navigate to the Identity Experience Framework section. In the menu on the left under Manage select Policy keys

Click Add, the popup blade on the right will show:

* Options: **Generate**
* Name: **TokenSigningKeyContainer**
* Key type: **RSA**
* Set activation date: **\<leave unchecked>**
* Set expiration date: **\<leave unchecked>**
* Key usage: **Signature**
* **Click Create**

Add another key:

Click Add, the popup blade on the right will show:

* Options: **Generate**
* Name: **TokenEncryptionKeyContainer**
* Key type: **RSA**
* Set activation date: **\<leave unchecked>**
* Set expiration date: **\<leave unchecked>**
* Key usage: **Signature**
* **Click Create**

****

Add another key:

Click Add, the popup blade on the right will show:

* Options: **Manual**
* Name: **RestApiUsername**
* Secret: **<\<username>>** _(set to something secret )_
* Set activation date: \<leave unchecked>
* Set expiration date: \<leave unchecked>
* Key usage: **Encryption**
* **Click Create**

Add another key:

Click Add, the popup blade on the right will show:

* Options: **Manual**
* Name: **RestApiPassword**
* Secret: **<\<password>>**  _(set to something secret and strong)_
* Set activation date: \<leave unchecked>
* Set expiration date: \<leave unchecked>
* Key usage: **Encryption**
* **Click Create**

****











