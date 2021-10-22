# Identity

Create an instance of the terminal and  navigate to the `/azure-quickstart/identity `directory&#x20;

initialize a new project and add dependencies

{% code title="/azure-quickstart/identity (terminal commands)" %}
```bash
# Init NPM
npm init -y

# Install Dependencies
npm i typescript ts-node --save-dev
npm i @types/node --save
npm i express express-validator grpc dapr-client
npx tsc --init

# Create src directory
mkdir src
```
{% endcode %}

edit `package.json` and add **build**, **start**, and **start:dev** as noted below (test should already exist)

{% code title="/azure-quickstart/identity/package.json" %}
```bash


...

  "scripts": {
    "build": "rimraf ./dist && tsc",
    "start": "npm run build && node dist/index.js",
    "start:dev": "npm run build && nodemon --ext \".ts,.js\" --watch \"./src\" --exec \"ts-node ./src/index.ts\"",
    "test": "echo \"Error: no test specified\" && exit 1"
  },

...
```
{% endcode %}

edit`  tsconfig.json  `to compile output to the dist/ directory by uncommenting and configuring values for **outDir** and **rootDir**

{% code title="/azure-quickstart/identity/tsconfig.json" %}
```bash
...

    "outDir": "./dist",                       /* Redirect output structure to the directory. */
    "rootDir": "./src",                       /* Specify the root directory of input files. Use to control the output directory structure with --outDir. */
...
  
...
```
{% endcode %}

Add additional NPM modules to set up basic services: `npm i express express-validator`

Create a build config..

In VS Code



