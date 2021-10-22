# Flag Structure & Storage

Flag Structure:

{% code title="<FileNameHere>.json" %}
```
{
      "FeatureFlags": [
            {
                  "Name": "<FlagName>",
                  "Description": "<FlagDescription>",
                  "Value": "<ValueFromAllowedValues>",
                  "AllowedValues": ["<ListOfAllowedValues>"],
                  "RetirementDate": "<RetirementDate[MM-DD-YYYY]>"
            }
      ]
}
```
{% endcode %}

Flag Storage:

* Files are stored in an Azure Blob container
* One file per environment (DEV, QA, and PROD)

Accessing Flags:

* Set the Public Access Level of the container containing the feature flag file to "Blob (anonymous read only access for blobs only)"
* Access the URL in Azure Blob file properties by clicking on the file in the Azure Blob container. (Ex URL: [https://\<StorageAccountName>.blob.core.windows.net/\<ContainerName>/\<FileName>.json](https://pwpblobcontainer.blob.core.windows.net/featureflags/Pathways\_Feature\_Flag\_QA.json))

