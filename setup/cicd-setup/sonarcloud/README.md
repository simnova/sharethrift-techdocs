# SonarCloud

## Introduction

SonarCloud helps to create a floor to the acceptable quality of code in a project. SonarCloud is free for public projects, and relatively affordable for private projects, it is a solution worth considering.



The following explains the process for adding SonarCloud to your project:

## Setup SonarCloud

### Create a SonarCloud _Account_ and _Organization_:

* Navigate to the [SonarCloud website](https://sonarcloud.io/) and create an account by logging in with **GitHub**&#x20;
* Click the + icon to the left of your name in the upper right of the screen, and&#x20;
  * Choose **Create an Organization** from the menu. _(The steps that follow are dependent on what you logged in with, refer to SonarCloud's documentation.)_

### Generate a Security Token

* While logged into SonarCloud website:
* Click your name in the upper right of the screen, and choose **My Account** from the menu.
  * Select the **Security** tab
  * Generate Tokens : **<\<recommend using your Azure DevOps project name e.g.: sharethrift>>**
  * Click the **Generate** button
    * Copy the token value that appears to a _secure_ note, _(It is only shown once, and you will need it later)_

### Create Projects

* While logged into SonarCloud website:
* Click the + icon to the left of your name in the upper right of the screen, and&#x20;
  * Choose **Analyze a new project** from the menu (you may see an error message, it can be ignored)
  * Click the **Create a project manually** link **** in the lower left of the screen.
  * The create project screen shows:
    * Organization: <\<select the organization you created earlier>>
    * Project Key: sharethrift-ui
    * Display Name: sharethrift-ui
    * Public / Private: <\<public for free>>
    * Click the **Set Up** button (ignore the screen that shows afterwards)
* Navigate to the newly created project
  * On the vertical navigation, choose **Branches**
  * Click on the **Gear** icon to the right of the master branch and choose **Rename Branch**
    * Rename to "**main**" and click the **Rename** button
  * **Quality Gates**
    * On the vertical navigation, choose Administration > Quality Gate
    * Choose the "Pathways" Quality Gate
* Repeat the process above with the following Project Key/ Display Names:
  * sharethrift-data-access





## Setup Azure DevOps

### Install SonarCloud plug-in into Azure DevOps

* In the **Azure DevOps** portal, navigate to your project's settings
* Choose **Service Connections** under Pipelines
* Click the **New Service Connection** button
* Search for **SonarCloud** and select it and click **Next**
* New Sonar Cloud Service Connection blade will show:
  * Sonar Cloud Token: **<\<paste Security Token generated earlier>>**
  * Service Connection Name: **sonarcloud**
  * Description: **<\<leave blank>>**
  * Security: **<\<leave checked>>**
  * Click the **Verify and save** button

## Add to VS Code



Configuration Process:

1. Install the [SonarLint Extension](https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarlint-vscode) into VSCode, and restart VSCode
2. Open your VSCode project&#x20;
3. Visit [SonarCloud](https://sonarcloud.io/) and log in using the **Azure DevOps** button.
4. Visit [your security page](https://sonarcloud.io/account/security/)
5. Under the section titled **Generate Tokens**, enter `vscode` and click the **Generate** button.
6. You will get a new token, copy the token value secure location we will use it later and you will not be able to retrieve it once it is generated.
7. Open Visual Studio Code, and navigate to the setting section
   * On Windows/Linux - File > Preferences > Settings
   * On macOS - Code > Preferences > Settings
8. Select User Settings and Search for SonarLint
9. Ensure that you select **User** directly under the search box so that you are configuring user settings.
10. Pick any of the settings and click \*\*Edit in settings.json
11. Paste the following into the settings.json file

    ```
    "sonarlint.connectedMode.connections.sonarcloud": [
        {
          "organizationKey": "<<YOUR COMPANY NAME>>",
          "token": "<<YOUR VSCODE TOKEN GOES HERE>>"
        }
      ],
    "sonarlint.connectedMode.project": {
        "projectKey": "<<YOUR PROJECT KEY>>"
      },
    ```
12. Replace the <\<YOUR VSCODE TOKEN GOES HERE>> with the _token_ you created earlier.
13. Replace the <\<YOUR COMPANY NAME>> with your _organization key_
    * You can look up the _organization key_ on the [organization key page](https://sonarcloud.io/account/organizations).
14. Replace the <\<YOUR PROJECT KEY>> with your _project key_.
    * You can look up the _project key_ under Administration > Projects Management on your organization's page.
15. **IMPORTANT!!** Sonarlint will prompt you to install the Adopt Open JavaSDK if you don't have java installed, please ensure you do this.
16. Restart VSCode and reopen your VSCode Project related to your ScratchOrg

The following is a sample settings.json file for reference:

```
{
    "sonarlint.connectedMode.connections.sonarcloud": [
        {
          "organizationKey": "mycompany",
          "token": "b33e974....76331859"
        }
      ],
    "sonarlint.connectedMode.project": {
        "projectKey": "myproject"
      },
    "sonarlint.ls.javaHome": "/Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home"
}
```

