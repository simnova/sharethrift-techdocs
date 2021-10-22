# Create AAD Applications

##

### Create AAD Application for UI

Create an AAD Application for the UI:

1. Go to the new [Azure portal - App registrations](https://portal.azure.com/#blade/Microsoft\_AAD\_RegisteredApps/) pane.
2. Click** + New Registration**
   1. If prompted choose **Register in this Directory**
3. Enter a "ShareThrift" for your application.
4. Under **Supported account types**, select **Accounts in any organizational directory and personal Microsoft accounts**.
5. Redirect URI
   1. Choose **Single-page application (SPA)** from the option list
   2. For the URI add the public URL from your CDN Endpoint, e.g https://sharethrift.azureedge.net)
6. Select **Register**.

Configure AAD Application for the UI

* In the AAD Application just created, navigate to Manage > **Authentication** in the navigation menu on the left.
* In Platform Configurations, look for the  **Single Page Application**
* in the section, **Redirect URIs**
  * Click** Add URI **and add:** http://localhost:5000/**
* Logout URL add:
  * (add the public URL from your CDN Endpoint, e.g https://sharethrift.azureedge.net) + /logged-out
* Implicit Grant (leave checkboxes unchecked)
* Live SDK Support: Yes
* Default Client Type: No
* Choose **Save** at the top of the screen.
* Copy the **Application (client id)** from the Overview panel of this Application to a text document, well use it shortly

### Create AAD Application for the API

Create an AAD Application for the API:

1. Go to the new [Azure portal - App registrations](https://portal.azure.com/#blade/Microsoft\_AAD\_RegisteredApps/) pane.
2. Click** + New Registration**
   1. If prompted choose **Register in this Directory**
3. Enter a "**ShareThrift**" for your application.
4. Under **Supported account types**, select **Accounts in any organizational directory and personal Microsoft accounts**.
5. Redirect URI (leave blank)
6. Select **Register**.

Expose an AAD Api / Add Client Application

* Under **Manage** > Select **Expose An Api**
* Application ID URI : Select **Set **and&#x20;
  * choose a value like api://sharethrift\<randomnumber>.com
  * click Save
* Scopes defined by this API : click **+ Add a scope, **the add a scope pane showed.
  * Scope Name: **access\_as\_user**
  * Who can consent: **Admins and users**
  * Admin consent display name: **Access ShareThrift API**
  * Admin consent description: **Allows the app to access the ShareThrift API as the signed-in user.**
  * User consent display name: **Access ShareThrift API**
  * User consent description: **Allow the application to access the ShareThrift API on your behalf.**
  * State:** Enabled**
  * Click **Add Scope **(the pane will disappear)
* Authorized Client Applications
  * Click **Add a client application**
  * Paste in the **Application (client id) **value from the AAD Application for the UI
  * Check the  the **api://sharethrift\<randomnumber>.com/access\_as\_user** scope you just created.
  * Click Save

### Create Custom Roles for UI and API

{% hint style="info" %}
Repeat the following for BOTH the UI and the API Applications, be sure to change the ID to a new GUID for each value each time.
{% endhint %}

* Under **Manage** > Select **Manifest**
* **Locate the area of the document with**
  * `"appRoles": [],`
* And replace it with:
  * (Be sure to update the <\<CHANGE TO NEW GUID>> with new, unique GUID values)

```javascript
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Moderator",
      "id": "<<CHANGE TO NEW GUID>>",
      "isEnabled": true,
      "description": "Moderators Have the ability reject items requested to be shared due to violations of policy.",
      "value": "Moderator"
    },
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Partner Technical Administrator",
      "id": "<<CHANGE TO NEW GUID>>",
      "isEnabled": true,
      "description": "Partner Technical Administrators are technical staff who have the ability to change SAML config and add/remove users.",
      "value": "PartnerTechAdmin"
    },
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Partner Billing Administrator",
      "id": "<<CHANGE TO NEW GUID>>",
      "isEnabled": true,
      "description": "Partner Billing Administrators are staff with ability to see and work with financial data.",
      "value": "PartnerBillingAdmin"
    },
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Partner Staff",
      "id": "<<CHANGE TO NEW GUID>>",
      "isEnabled": true,
      "description": "Partner Staff are staff with ability manage with listings.",
      "value": "PartnerStaff"
    },
        {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Public User",
      "id": "<<CHANGE TO NEW GUID>>",
      "isEnabled": true,
      "description": "Public Users are general users.",
      "value": "PublicUser"
    }
  ],
```

### Add a user to the roles

Add A user to the role for both applications

* Navigate to Active Directory in the Azure Portal&#x20;
* Under **Manage** > Select **Enterprise Applications**
* Select **ShareThrift** from the list of applications
* Under **Manage** > Select **Users and groups**
* Choose** Add Users**
* For users - select** yourself**
* Select Role: select** Moderator**
* Click** Assign**







&#x20;&#x20;

#### Resources:

* Microsoft
  * [MSAL Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki)
  * [MSAL Readme Docs (newest)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser/docs)
  * [Add app roles in your application](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
  * DON"T USE IMPLICIT GRANT - [Read Why](https://developer.microsoft.com/en-us/microsoft-365/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/)
  * [Great tutorial ](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/tree/master/chapter1)





