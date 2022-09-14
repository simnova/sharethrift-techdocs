---
description: Setup instruction for Azure Event Hub
---

# Azure Event Hub

## Getting Started

Azure Event Hub provides a simple, scalable solution to stream millions of events per second. It provides seamless integration with Azure services like Azure function. Below are the steps to configure Azure Event Hub.

### Create Event Hubs Namespace

1. In the Azure Portal - search for`Event Hubs` and select it. It will take you to the Event Hubs blade.
2. Click Create event hub button, you will be displayed the Create `Event Hub Namespace` blade

{% hint style="info" %}
&#x20;Depending on your cost structure, you may want to create two different namespaces one for preproduction (qa) and another for production. e.g.

* `sharethrift-qa-event-hub`
* `sharethrift-prod-event-hub`
{% endhint %}

```
Subscription: <<your subscription>>
Resource Group: sharethrift-rg

Namespace name: sharethrift-qa-event-hub
Location: east US 2
Pricing tier: Standard (ideal for a mid-size organization)
Throughput Units: 1
Enable Auto-Inflate: keep unchecked
```

3\. Click: Review + create (wait for the validation to complete), then click "Create", you will be taken to the overview page, click the "Go to resource" button.

### Create Event Hubs Instance

An Event Hub Namespace can have multiple Event Hubs. Once Event Hubs Namespace is created, perform the following steps to create an Event Hub specific to your project. Click on Event Hubs under Entities and create `Event Hub` specific to your project and provide the below details.

```
Name: sales
Partition Count: 1
Message Retention: 1
Capture: Off
```

Click "Create" to create the Event Hub specific to your project.

### Consumer Groups

An Event Hub can have multiple consumer groups. This is a good practice to decouple the consumer systems. For example, a Sales Events can have two different consumers:

1. Inventory System
2. Payment System

To create Consumer Groups under a specific Event Hub,&#x20;

1. Select the "Sales" Event Hub.
2. Under "**Entities**" section, select "**Consumer groups**"
3. By default, there would be "**$Default**" consumer group.
4. Click "+ Consumer group"
5. Enter the name of Consumer, "**Inventory**" and click **Create** button
6. Enter another Consumer, "**Payment**" and click **Create** button

### Security

By default, "Event Hubs Namespace" will have primary/secondary keys along with its connection strings auto-generated under **Shared access policies** with Policy "**RootManageSharedAccessKey**" which is common to all Event Hub Instances. For added security, it is recommended to create a **Shared access policy** for each Event Hubs Instance manually. Below are the steps...

1. Select Event Hubs Instance **Sales**
2. Under **Settings**, select **Shared access policies**
3. Click **+Add**
4. Give Policy name**: SalesSharedAccessKey**
5. Select **Manage** and click **Create** button
6. It will generate New Primary/Secondary Keys along with its connection string which can be copied by selecting **SalesSharedAccessKey**
7. Use the **connection string** in your **producer / consumer** systems
