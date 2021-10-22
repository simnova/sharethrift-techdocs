# Workstation Installs

## Don't Skip the Prerequisites&#x20;

While the following may seem tedious, going through this process will save you hours of headache down the road.

### Workstation Setup

* Node- > NVM - [Windows](https://github.com/coreybutler/nvm-windows)/[Mac](https://github.com/nvm-sh/nvm)&#x20;
  * Windows - ([reference](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/))
    * Uninstall any versions of Node.js (from programs)
    * Delete any Node.js installation directories (ex: C:\Program Files\nodejs)
    *   Delete the existing npm install location (such as `C:\Users\<user>\AppData\Roaming\npm `

        `C:\Users\<user>\AppData\Roaming\npm_cache`)
    * Download the latest release of [NVM-Windows](https://github.com/coreybutler/nvm-windows/releases) (nvm-setup.zip)
    * Extract nvm-setup.zip and run nvm-setup.exe
    * After install, run cmd:
      * nvm install {version number} where version number is lts version of nodejs. (ex current latest: nvm install 14.17.5)
    * Run cmd: nvm use {version number}&#x20;
    * Ensure that [Node](https://nodejs.org/en/) (LTS Version) is what is reported
      * `node --version`&#x20;
        * should return 14.17.5 or greater
* [GIT](https://git-scm.com/downloads)&#x20;
  * `git --version`
    * should return 2.24.3 or greater
* [.NET ](https://dotnet.microsoft.com/download)5 **SDK**- required for Function Extensions
  * dotnet --version
    * should return 5.0.400 or greater
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools)
  * `func --version`
    * should return 3.0.347 or greater
  * [Install Guide](https://github.com/Azure/azure-functions-core-tools#installing)
    * to upgrade: (mac)
      * brew upgrade azure-functions-core-tools@3
* [CosmosDB Emulator](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator?tabs=ssl-netstd21)
  * Start up using command line or batch file:
    * `"C:\Program Files\Azure Cosmos DB Emulator\Microsoft.Azure.Cosmos.Emulator.exe" /EnableMongoDbEndpoint=4.0`
* [Visual Studio Code](https://code.visualstudio.com)
  * `code --version`
    * should return 1.49.1 or greater
  *   Install these Visual Studio Code Plugins:

      * [Azure Static Web Apps (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
      * [Azure Functions ](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
      * [Visual Studio Intellicode](https://marketplace.visualstudio.com/items?itemName=VisualStudioExptTeam.vscodeintellicode)
      *   [Azurite](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azurite) - storage emulator




*
*

#### Optional Items

* Chrome Extensions&#x20;
  * [Apollo Dev Tools](https://chrome.google.com/webstore/detail/apollo-client-developer-t/jdkknkkbebbapilgoeccciglkfbmbnfm)
  * [React Dev Tools](https://fb.me/react-devtools)
* VS Code Extensions&#x20;
  * [Apollo GraphQL](https://marketplace.visualstudio.com/items?itemName=apollographql.vscode-apollo)
  * [GIT Lens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)
  * [GIT Pull Requests and Issues](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-pull-request-github)
  * [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)



