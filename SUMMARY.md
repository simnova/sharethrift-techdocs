# Table of contents

* [Introduction](README.md)

## Initial Setup <a href="#setup" id="setup"></a>

* [Workstation Installs](setup/workstation-setup.md)
* [Codebase](setup/introduction/README.md)
  * [Directory Structure](setup/introduction/directory-setup.md)
  * [User Interface Project](setup/introduction/user-interface/README.md)
    * [Configuring StoryBook](setup/introduction/user-interface/configuring-storybook.md)
    * [Configure Tailwind](setup/introduction/user-interface/configure-tailwind.md)
    * [Configure Craco](setup/introduction/user-interface/configure-craco.md)
    * [-Architectural Decision Log](setup/introduction/user-interface/architectural-decision-log.md)
  * [Data Access Project](setup/introduction/data-access.md)
  * [Untitled](setup/introduction/untitled.md)
  * [Full Stack Debugging](setup/introduction/full-stack-debuggons.md)
  * [Creating GitHub Project](setup/introduction/creating-github-project.md)
* [Infrastructure](setup/infrastructure-setup/README.md)
  * [Create AAD Tenant](setup/infrastructure-setup/create-aad-tenant.md)
  * [Resource Group](setup/infrastructure-setup/resource-group.md)
  * [Create AAD B2C Instance](setup/infrastructure-setup/create-aad-b2c-instance/README.md)
    * [Identity Experience Framework](setup/infrastructure-setup/create-aad-b2c-instance/identity-experience-framework.md)
    * [Configure Session Behavior](setup/infrastructure-setup/create-aad-b2c-instance/configure-session-behavior.md)
  * [Storage Account & CDN](setup/infrastructure-setup/create-azure-storage-account/README.md)
    * [CDN Rules](setup/infrastructure-setup/create-azure-storage-account/configure-azure-cdn.md)
    * [Configure Azure BLOB Storage](setup/infrastructure-setup/create-azure-storage-account/configure-azure-blob-storage.md)
  * [App Insights](setup/infrastructure-setup/app-insights/README.md)
    * [Create AppInsight Account](setup/infrastructure-setup/app-insights/create-appinsight-account.md)
    * [Apollo GraphQL App Insights Configuration](setup/infrastructure-setup/app-insights/apollo-configuration.md)
  * [CosmosDB](setup/infrastructure-setup/untitled.md)
  * [Twilio SendGrid](setup/infrastructure-setup/twilio-sendgrid.md)
  * [KeyVault](setup/infrastructure-setup/keyvault.md)
  * [Function](setup/infrastructure-setup/configure-azure-function.md)
  * [Function App Settings](setup/infrastructure-setup/function-app-settings.md)
  * [Front Door](setup/infrastructure-setup/configure-azure-frontdoor.md)
  * [DevOps](setup/infrastructure-setup/devops.md)
  * [Optional Items](setup/infrastructure-setup/optional-items/README.md)
    * [Azure Data Factory](setup/infrastructure-setup/optional-items/azure-data-factory.md)
  * [Azure Event Hub](setup/infrastructure-setup/azure-event-hub.md)
* [CICD and Source Control](setup/cicd-setup/README.md)
  * [Untitled](setup/cicd-setup/untitled.md)
  * [Azure DevOps](setup/cicd-setup/azure-devops.md)
  * [SonarCloud](setup/cicd-setup/sonarcloud/README.md)
    * [Incorporate into Yaml](setup/cicd-setup/sonarcloud/incorporate-into-yaml.md)
  * [User Interface YAML](setup/cicd-setup/user-interface-yaml.md)
  * [CICD for Data Access](setup/cicd-setup/cicd-for-data-access/README.md)
    * [Create Pipeline](setup/cicd-setup/cicd-for-data-access/create-pipeline.md)
    * [Data Access YAML](setup/cicd-setup/cicd-for-data-access/yaml-for-functions.md)

## Application Structure <a href="#application-design" id="application-design"></a>

* [Connect Apollo](application-design/connect-apollo/README.md)
  * [Apollo Overview](application-design/connect-apollo/apollo-overview.md)
  * [Create Apollo Component](application-design/connect-apollo/create-apollo-component.md)
* [MongoDB Integration](application-design/mongodb-integration/README.md)
  * [Mappings](application-design/mongodb-integration/mappings.md)
  * [Directory Structure](application-design/mongodb-integration/directory-structure.md)
  * [Apollo Connection](application-design/mongodb-integration/apollo-connection.md)
  * [Models](application-design/mongodb-integration/models.md)
  * [Queries Mutations and Subscriptions](application-design/mongodb-integration/queries-mutations-and-subscriptions.md)
  * [Caching Reponses](application-design/mongodb-integration/caching-reponses.md)
* [Integrating GraphQL Tools](application-design/integrating-graphql-tools/README.md)
  * [GraphQL Code Generator](application-design/integrating-graphql-tools/graphql-code-generator.md)
* [Feature Flags](application-design/feature-flags/README.md)
  * [Flag Structure & Storage](application-design/feature-flags/flag-structure-and-storage.md)
  * [Website Integration](application-design/feature-flags/website-integration.md)
  * [Apollo Integration](application-design/feature-flags/apollo-integration.md)
  * [Tips and Techniques](application-design/feature-flags/tips-and-techniques.md)
  * [Alternative Approaches](application-design/feature-flags/alternative-approaches.md)
* [React Router](application-design/react-router.md)
* [Adding Authentication](application-design/adding-authentication/README.md)
  * [Create AAD Applications](application-design/adding-authentication/aad-application.md)
  * [Configure AAD For External Identities](application-design/adding-authentication/configure-aad-for-external-identities.md)
  * [Adding MSAL And React](application-design/adding-authentication/adding-msal-and-react.md)
  * [Add MSAL to the build](application-design/adding-authentication/add-msal-to-the-build.md)
  * [Add MSAL to ApolloClient](application-design/adding-authentication/ad.md)
  * [Add MSAL to ApolloServer](application-design/adding-authentication/add-msal-to-apolloserver.md)
* [Ant Design](application-design/ant-design.md)
* [Jest Tests](application-design/jest-tests.md)

***

* [Azure Active Directory Business-to-Consumer (AD B2C)](azure-active-directory-business-to-consumer-ad-b2c/README.md)
  * [Introduction](azure-active-directory-business-to-consumer-ad-b2c/introduction.md)
  * [How to navigate through AD B2C documentation](azure-active-directory-business-to-consumer-ad-b2c/how-to-navigate-through-b2c-documentation.md)
  * [Localization](azure-active-directory-business-to-consumer-ad-b2c/localization.md)
  * [Abbreviations](azure-active-directory-business-to-consumer-ad-b2c/abbreviations.md)
  * [Azure AD B2C Extension](azure-active-directory-business-to-consumer-ad-b2c/azure-ad-b2c-extension.md)
* [Cognitive Search](cognitive-search.md)
* [Cost Analysis](cost-analysis.md)
* [Technical Architecture](technical-architecture/README.md)
  * [Identity and Access Control](technical-architecture/identity-and-access-control.md)

## Adding Functionality

* [Google Analytics](adding-functionality/google-analytics/README.md)
  * [Create Analytics](adding-functionality/google-analytics/create-analytics.md)
* [DAPR](adding-functionality/dapr/README.md)
  * [DAPR setup](adding-functionality/dapr/dapr-setup.md)
  * [DAPR Services (ignore for now)](adding-functionality/dapr/dapr-services/README.md)
    * [Identity](adding-functionality/dapr/dapr-services/identity.md)

## Patterns and Practices

* [Idempotent Messages](patterns-and-practices/itempotent-messages.md)
* [Pathways](patterns-and-practices/pathways.md)

## Open Items

* [Issue Tracking](open-items/issue-tracking.md)

***

* [Helpful Resources](helpful-resources.md)

## DDD

* [Page 1](ddd/page-1.md)

## Experimental

* [StaticWebApp](experimental/staticwebapp.md)
* [Azure Maps](experimental/azure-maps.md)
