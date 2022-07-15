---
description: (~15 minutes)
---

# User Interface Project

The UI Project will rely on the following:

* **React** -The create-react-app is sufficient starter for this MVP project )
* **Apollo Client** - Allows the UI to interact with the Apollo GraphQL data tier,&#x20;
* **React Router** - An easy to use router for react.
* **Ant Design** - A versatile UI framework for React.&#x20;
* **Storybook**&#x20;

Begin by initializing the project with these libraries.&#x20;

From the `azure-quickstart` directory open a command prompt or console and issue the following commands:

{% code title="Terminal / Command Propt" %}
```bash
npm uninstall -g create-react-app
npx create-react-app ui --template typescript
cd ui

##CRACO IS USED FOR LESS (AntDesign Customization)
npm i @craco/craco@7.0.0-alpha.3 
npm i -D craco-less@2.1.0-alpha.0
npm i process browserify-zlib stream-browserify util buffer assert crypto-browserify

## Tailwind CSS is handy for UI design
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p #-p creates a postcss.config.js file

npm install @apollo/client graphql
npm install react-router-dom # ?? @types/react-router-dom
npm install antd


#npm i prop-types @types/prop-types
npx sb init --builder webpack5
##npx -p @storybook/cli sb init
npm i -D @storybook/addons @storybook/theming chromatic
##npm i -D storybook-addon-playwright
```
{% endcode %}

In order to enable VS code debugging, the launch.json file will need to be modified.

Launch VS Code:

```bash
code .
```

Create launch.json file

In VSCode, In the left panel, go to the run tab. Click **create a launch.json file** and select **Chrome** from the dropdown.

![](<../../../.gitbook/assets/image (4).png>)

Edit the **launch.json** file

{% code title="azure-quickstart/ui/.vscode/launch.json" %}
```javascript
{
  // Use IntelliSense to learn about possible attributes.
  // Hover to view descriptions of existing attributes.
  // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Launch Chrome",
      "request": "launch",
      "type": "pwa-chrome",
      "url": "http://localhost:8080",
      "webRoot": "${workspaceFolder}/src",
      "preLaunchTask": "npm: start",
      "postDebugTask": "kill-terminal",
      "sourceMaps": true,
      "sourceMapPathOverrides": {
        "webpack://./src/*": "${webRoot}/*"
      }
    },
    {
      "type": "chrome",
      "request": "launch",
      "name": "Launch Storybook",
      "url": "http://localhost:8081",
      "webRoot": "${workspaceFolder}/src",
      "preLaunchTask": "storybook",
      "postDebugTask": "kill-terminal",
      "sourceMaps": true,
      "sourceMapPathOverrides": {
        "webpack://./src/*": "${webRoot}/*"
      },
    }
  ]
}
```
{% endcode %}

Add a **tasks.json** file

{% code title="azure-quickstart/ui/.vscode/task.json" %}
```javascript
{
  "version": "2.0.0",
  "tasks": [
    {
      "type": "npm",
      "script": "start",
      "group": {
        "kind": "test",
        "isDefault": true
      },
      "isBackground": true, // This prevents the launch.json to wait for the completion of the task
      "problemMatcher": {
        "owner": "custom", // This is not needed but, required by the problemMatcher Object
        "pattern": {
          "regexp": "^$" // This is not needed but, required by the problemMatcher Object
        },
        "background": {
          "activeOnStart": true,
          "beginsPattern": "Compiling...", // Signals the begin of the Task
          "endsPattern": "Compiled .*" // Signals that now the initialization of the task is complete
        }
      }
    },
    {
      "type": "shell",
      "command": "npx start-storybook -p 8081",
      "label": "storybook",
      "group": {
        "kind": "test",
        "isDefault": true
      },
      "isBackground": true, // This prevents the launch.json to wait for the completion of the task
      "problemMatcher": {
        "owner": "custom", // This is not needed but, required by the problemMatcher Object
        "pattern": {
          "regexp": "^$" // This is not needed but, required by the problemMatcher Object
        },
        "background": {
          "activeOnStart": true,
          "beginsPattern": ".*storybook\/react*", // Signals the begin of the Task
          "endsPattern": ".*Storybook.*started.*" // Signals that now the initialization of the task is complete
        }
      }
    },
    {
      "label": "kill-terminal",
      "type": "process",
      "command": "${command:workbench.action.terminal.kill}"
    }
  ]
}
```
{% endcode %}

Add a **.env** file

{% code title="azure-quickstart/ui/.env" %}
```javascript
PORT=8080
BROWSER=none
```
{% endcode %}

~~Navigate to the source control tab of VS Code, locate the.env file and choose "Add to .gitignore"~~

In a multi-developer scenario developers will use `.env.local`, we'll only add default and non-sensitive values to the .env file

Go Ahead and launch the website by using VSCode Debugger.

#### Additional Reading:

* Create React App - [Getting Started](https://create-react-app.dev/docs/getting-started)
* React Router - [Documentation](https://reactrouter.com/web/guides/quick-start)
* Apollo Client - [Get Started](https://www.apollographql.com/docs/react/get-started/)
* Ant Design - [Introduction](https://ant.design/docs/react/introduce)
