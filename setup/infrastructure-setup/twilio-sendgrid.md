# Twilio SendGrid

* Navigate to the Create [SendGrid Account ](https://portal.azure.com/#create/SendGrid.sendgrid\_azure)in the Azure Portal
* Choose the appropriate subscription and the rg-sharethrift resource group.
* Location : **(US) East US 2**
* Account Details
  * Name: **sharethrift**
  * Password: <\<use a super strong password>>
  * Pricing Tier: (leave the default : free)
* Contact Details - Fill in your personal information (Recommended to use personal email)
* Click : **Review + Create**
* Click: **Create **(wait for creation)
* Click: **Go to Resource**
* In the Sendgrid account just created, under Overview -> Click **Manage**
  * You may be prompted for send confirmation email, go ahead and allow for that and verify your email.
  * Choose - Create a Single Sender
    * Fill in an email (choose a personal email to start)
    * Complete the form
    * You will receive an email asking you to comfirm the email you entered. Return to Sender Verification
  * In the sendgrid navigation on the left, choose API Keys, you will be presented with the Create API Key blade
    * API Key Name: **sharethrift**
    * API Key Permissions: **Restricted Access**
      * Enable:  **Mail Send**
    * Click **Create & View**
    * You will be presented with the API Key, Save this for use later.
