# Configure AAD For External Identities

One of the requirements is to allow the general public to log into the application, this can be achieved by using AAD self-service sign-up for External Identities.

Microsoft's Documentation will help out here, follow the steps in the articles below to get going:

* [Add a self-service sign-up user flow to an app (Preview)](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/self-service-sign-up-user-flow#select-the-layout-of-the-attribute-collection-form)
  * Be sure to follow the procedure [Add Facebook as an identity provider for External Identities](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/facebook-federation) as called out in the instructions
  * When selecting the application, be sure to select the ShareThrift UI App you created earlier.

It is important to note that the user experience for signing up is less than ideal at the moment (as the user has to look for a small link in the login dialog to create an account) but as this is a preview feature we can expect the experience to get better.



![Less than ideal user experience.](<../../.gitbook/assets/image (5).png>)

If you happen to have AAD Premium, you can change out the logo and background to make it more apparent to the user that they're not logging into Microsoft, but instead to your application/organization and would likely slightly lead users to click the "Create one" link.&#x20;



Unfortunately it doesn't appear that you can deep link into the "Create one"

![](<../../.gitbook/assets/image (9).png>)

## Customizing and validating additional details

We can add a validation layer before an account is created for the user. We would do this in order to prevent unwanted users from signing up to our website.

In order to do so, we need to create an API Connector and hook it into our user flow. We will also need a function app to validate it against as we will need to create a username and password in the API Connector.

### Creating API Connector

1. In Azure go to: Manage Azure Active Directory -> External Identities -> All API connectors
2. Add a New API connector
   1. Display Name: a unique name that will be used to hook into the user flow
   2. Endpoint URL: Endpoint in which we will validate the information. The API Connector will call onto this endpoint and send the requested data to it. (Our function app)
   3. Username: name in which we will validate against. (This should be checked against in the function app via environment variables, ENSURE the username does not have any colons)
   4. Password: password in which we will validate against. (This should be a super strong password and needs to exist in the function app via environment variables, ENSURE the password does not have any colons)
3. Save the API Connector

### Link API Connector to User Flow

1. In Azure go to: Manage Azure Active Directory -> External Identities -> User flows
2. Select the user flow in which you want to use the API Connector
3. Go to API Connectors
4. Before creating the user: (select the API Connector created earlier)
5. Save

### (Optional) Add User Attributes

We may want to validate against certain attributes.

We can add new attributes:

1. Manage Azure Active Directory -> External Identities -> Custom user attributes
2. Add
   1. Name: Name of value, this will come back in the payload
   2. Date Type: Type of the value
   3. Description: Describe what the value is used for
   4. Create

![](<../../.gitbook/assets/image (12).png>)

We can choose which attributes we want to collect from:&#x20;

1. Manage Azure Active Directory -> External Identities -> User flows
2. Select the user flow to modify
3. Go to User attributes
4. Select which items you want to ask the user (You will see custom and built in attributes)
5. Save

![](<../../.gitbook/assets/image (11).png>)

### Notes:

* Accounts cannot use domain level email addresses
* Facebook accounts must be verified

#### Resources:

* Microsoft
  * [Build 2020 Session introducing self-serve sign-up for external identities.](https://myignite.microsoft.com/sessions/1d0a154a-1701-4cce-adae-f1f3febd0d25)
* ISSUE!
  * [Issue with MSAL & External Identities - Difficulty with Roles](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/2445)
