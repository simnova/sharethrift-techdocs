# Infrastructure

Azure Setup:

After completing this section the following items will be set up and configured:

* Common
  * Resource Group - a container for everything created in Azure for the project.
* User Interface&#x20;
  * Azure Storage Account
    * Azure CDN (to protect visitors and provide faster access to the website)
* SRE&#x20;
  * Azure Monitor / App Insights
* Data Access&#x20;
  * Azure Function
    * Azure Front Door (to protect the Azure Function and to provide geo-redundancy )
  * CosmosDB - database
  * SendGrid - for sending transactional emails
  * KeyValut
* CICD
  * DevOps
  * SonarCloud&#x20;
