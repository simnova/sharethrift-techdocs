---
description: Welcome to the Azure Serverless Guided Walkthrough
---

# Introduction

This guide is to help set up the necessary infrastructure for a serverless project leveraging resources in from the Azure cloud offering.

This guide will provide guided instructions to develop a fully functional minimal viable product for a sample business case running in Azure.

A goal of this this sample case study is to allow the entire project to run within the Azure monthly credit associated with MSDN Enterprise subscribers.

## Sample Business Case&#x20;

This quick start will explore a sample business case of ShareThrift; an organization who's goal is to help reduce waste by encouraging the growth of the sharing economy allowing sharing of all sorts of goods such as tools, board games etc. among its members and partner organizations. ShareThrift desires to develop a platform that enables for sharing of goods, either free or paid, and to make the platform widely available to all different types of uses. ShareThrift's business model is to be self-sustaining by charging a fee for boosted listings on its platform.

{% hint style="info" %}
To Understand the Business Case and Full Requirements Visit the [ShareThrift  Notion site](https://simnova.notion.site/simnova/ShareThrift-Project-Charter-d38fba50b9644c71805d000a2809de23).
{% endhint %}

There are a number of different sharing models to be supported, allowing for individual to individual, as well as allowing for partners. Partners can be existing companies such as tool rental companies or the platform may help to spur new businesses such as short term purse rentals.

ShareThrift is a bootstrapped startup which needs be nimble and quickly respond to its users, it needs to focus on adapting and wants an infrastructure that can adapt to growth and have a linear cost model that will allow the company to grow without the underlying infrastructure cost hampering it's ability to grow.

ShareThift is looking to develop a minimal viable product (MVP) to prove its business model.

### Technical Goals

The CIO of ShareThrift, has asked you, the newly hired lead developer to deliver this MVP and has added the following technical goals:

Project Goals:

* Deliver a low-cost, scalable, secure and geo-redundant web app, which is easy to develop and maintain and supports continuous delivery, with a linear scaling cost model.
* Allow for different login types:
  * Partners with Staff (B2B)
    * Allow partners to integrate with AAD to provide single sign-on to their staff and allow their IT staff to manage accounts  and system access.
  * Corporate Staff&#x20;
    * Allow corporate staff manage product lists and to moderate the system.
  * Direct Customers (B2C)
    * Allow individuals to share items to each other
* Allow for system reporting for both internal staff as well as for partners and individuals.

Future Goals:

* Need to support a mobile application if the web-based MVP platform shows success.

### Technical Implementation

In working with the development team, you've landed on the following as the technology stack to meet the needs of ShareThrift.

Technology Stack:

* Front End
  * React
    * AntDesign (UI Framework)
  * Apollo GraphQL
* Serverless Backend
  * Azure Functions
    * Apollo GraphQL Server
* Data Tier
  * MongoDB (Azure CosmosDB)

Future Goals:

* Need to support a React Native mobile application

&#x20;Supporting Infrastructure:

* GitHub for source control
* Azure DevOps for CICD
* Azure Active Directory for internal staff, B2B and end user logins and authentication.
* Twilio Sendgrid for Transactional Emails
* SonarCloud for Static Analysis
* Azure Storage static website  + Azure CDN&#x20;
  * ([Azure Static Web Apps](https://azure.microsoft.com/en-us/services/app-service/static/) are not considered at this point as that functionality is in preview at the time of writing)
* Azure Cosmos - MongoDB Driver
* Azure Front Door +Azure Functions
* PayPal - for payments



