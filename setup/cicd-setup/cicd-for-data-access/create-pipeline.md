# Create Pipeline



Head over to [Azure DevOps](https://dev.azure.com/ECFMGTFS/BusinessTransformation) >> Pipelines

Click **New pipeline** button

Select **GitHub**

Choose the repository for the new pipeline (if you can't find it, change **My repositories** to **All repositories**)

Select **Existing Azure Pipelines YAML file**

On the Select an existing YAML file screen choose:

* Branch: main
* Path: _<\<path to azure-pipelines.yml>>_

Click **Continue**

Click **Run**

It should fail on the first run because we haven't created the environment(s) yet



TO DO: Review below steps for possible update

Save and run (We will be overwriting this file later on)

Select the pipeline, Click the Edit button.

Click the vertical "..." (More actions) button next to Run in the upper right corner and select Triggers from the dropdown menu

Select Pull Request Validation

Check "Override the YAML pull request trigger from here"

Under Path Filters click "Add"&#x20;

Type "Include" &#x20;

Path Specification : /data-access

Choose Save and Queue



## Uncheck Shallow Fetch

* On the pipeline page you just created, click **Edit** in the upper right
* Click on the **3 dots** icon in the upper right and choose **Triggers**
* Click on the **YAML** tab
* Click **Get sources**
* Ensure the **Select a source** section has **GiHub** highlighted
* Scroll down and uncheck **Shallow fetch**
* Save your changes







