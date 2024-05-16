# Create Pipeline

## Create Pipeline in Azure DevOps

* Head over to [Azure DevOps](https://dev.azure.com/ECFMGTFS/BusinessTransformation) >> Pipelines
* Click **New pipeline** button
* Select **GitHub**
* Choose the repository for the new pipeline (if you can't find it, change **My repositories** to **All repositories**)
* Select **Existing Azure Pipelines YAML file**
* On the Select an existing YAML file screen choose:
  * Branch: main
  * Path: _<\<path to azure-pipelines.yml>>_
* Click **Continue**
* Click **Run**

**NOTE: The first run may **<mark style="color:red;">**FAIL**</mark>** if you have not already created the environment(s) yet, or unchecked shallow fetch**

## **Create Environment**

* Head over to [Azure DevOps](https://dev.azure.com/ECFMGTFS/BusinessTransformation) >> Pipelines >> Environments
* Click **New environment**
* Fill out the **Name** and **Description**
* For **Resource** choose **None**
* Click **Create**

## Uncheck Shallow Fetch

* On the pipeline page you just created, click **Edit** in the upper right
* Click on the **3 dots** icon in the upper right and choose **Triggers**
* Click on the **YAML** tab
* Click **Get sources**
* Ensure the **Select a source** section has **GiHub** highlighted
* Scroll down and uncheck **Shallow fetch**
* Save your changes







