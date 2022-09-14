---
description: (~time varies, minimum 10 minutes)
---

# User Interface YAML

Steps:

* Define Environments (QA / PROD)
  * Gate Environments with Approvals
* Define YAML&#x20;
  * Create 2 Builds (QA/Prod)
  * Store QA /Prod output as artificats
  * Define&#x20;
    * QA stage
    * Prod stage
  * Steps



## Define Environments

* In your Azure DevOps project navigate to Pipelines > Environments
  * Click the New Environment button at the top of the screen, the New environment dialog will show.
    * Name: **ui-qa**
    * Description: **The QA environment for the static website**
    * Resource: **none**
    * Click **Create** (you will be taken to the new environment)
  * In the upper right corner, next to the Add resource button, click the **vertical ... button** and choose **Approvals and Checks**, the Approvals and checks screen will be displayed.
    * **Add Approvers**
      * Click **+** in the upper right, the _Add check_ dialog will show
      * Choose **Approvals**
      * Click **Next**.
        * Approvers: <\<Choose specific users or groups who will be permitted to approve deployments to this environment. You can select just yourself at this time>>
        * Instructions: **You must approve a deployment to the QA environment.**
        * Advanced
          * Minimum number of approvals required: **1**
          * Allow approvers to approve their own runs: **(checked)**
        * Control options:
          * Timeout: **30 Days**
        * Click **Create**
    * **Add Exclusive Lock**
      * Click **+** in the upper right, the Add check dialog will show
        * Choose **Exclusive Lock**
        * Click **Next**,&#x20;
        * Accept defaults and Click **Create**
        * Click **<-**  (back arrow) at the top of the screen to return to the environment again.
        * Click **<-**  (back arrow) at the top of the screen to return to the environments list.
* Repeat the same process above but replace _**QA**_ with _**Prod**_

Add 3 files to your repo - check in:

* ui/azure-pipelines.yml
* ui/build-pipeline/build-static-website.yml
* ui/build-pipeline/deploy-static-website.yml



* In your Azure DevOps project, navigate to Pipelines
  * Click **New Pipeline**
  * When asked _Where is your code?_, choose **GitHub**
  * (You may be prompted to authorize access to your GitHub Account, if needed, do so)
  * Select a repository:  **<\<choose the appropriate repo where you've stored your code.>>**
  * Configure your pipeline: **Existing Azure Pipelines YAML file** (a blade will show)
    * Select an existing YAML file
    * Branch: **master**
    * Path: **/ui/azure-pipelines.yml**
    * Click **Continue -** you will be able to _Review your pipeline YAML_
  * Click **Variables -> New Variable**
    * **TODO: Document all variables to add**
    * **Run -> Save (wait while pipeline is created)**
  * You will be prompted to select your Azure DevOps Project you created earlier (sharethrift)
  * Configure: Node.js
* When running the YAML navigate to the job, it will note that the pipeline needs access to a resource before it can continue.
  * Click View, a dialog will show, click Permit, another dialog will show requesting permissions, click Permit again

```yaml
# Node.js
# Build a general Node.js project with npm.
# Add steps that analyze code, save build artifacts, deploy, and more:
# https://docs.microsoft.com/azure/devops/pipelines/languages/javascript

trigger:
 paths:
   include:
     - ui
 branches:
   include:
     - main

pr: none
  
variables:
    system.debug: true

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '12.x'
  displayName: 'Install Node.js'

- script: |
    npm install
    npm run build
  displayName: 'npm install and build'
  workingDirectory: ui
  env:
    INLINE_RUNTIME_CHUNK: false

- task: AzureCLI@2
  displayName: Copy to Blob
  inputs:
    azureSubscription: 'Visual Studio Enterprise(1e23aa72-fb0d-424b-a48c-0de4916aee72)'
    scriptType: 'pscore'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az storage blob upload-batch --destination `$web --account-name sharethrift --source . --auth-mode login
      az cdn endpoint purge --profile-name sharethrift -g rg-sharethrift -n sharethrift --content-paths "/*"
    addSpnToEnvironment: true
    workingDirectory: 'ui/build'
```

Some notes:

* NODETOOL - added to ensure the correct version of Node is installed on the build server
* INLINE\_RUNTIME\_CHUNK - this is required due to the CDN security rules
* AZURE CLI -&#x20;
  * Copies files to blob storage
  * Purges the CDN so that it serves the newly deployed blob files

Resources

* [CloudBees Intro to YAML](https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started/)
* [Tutorialspoint YAML Docs](https://www.tutorialspoint.com/yaml/index.htm) - Very Detailed
* [ContentLab Azure DevOps Yaml Article ](https://contentlab.io/writing-modern-ci-cd-pipelines-with-azure-devops/)- Excellent article on how to organize YAML w/AzureDevOps
* [Microsoft's YAML Documentation - Variables](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops\&tabs=yaml%2Cbatch#understand-variable-syntax) - This is a little confusing..
* [Microsoft's YAML Documentation - Templates ](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/templates?view=azure-devops)
