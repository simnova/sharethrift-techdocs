# Create Pipeline



Add new pipeline

Select github repository

Authorize github permissions

Select the repository in question

Select Node.js with React

(for Monorepo) - update the location of azure-pipelines.yml file to your project dir



(for Monorepo) - update the location of azure-pipelines.yml file to your project dir



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







