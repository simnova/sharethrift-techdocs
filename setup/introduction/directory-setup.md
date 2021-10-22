# Directory Structure

## Overview

This project will be configured as a Monorepo ([not Monlith](https://blog.nrwl.io/misconceptions-about-monorepos-monorepo-monolith-df1250d4b03c)) allowing the full stack of the application be stored in a single repository.

The full codebase will be runnable locally from a single instance of VSCode.

The project structure will  look like the following after completing this section

```bash
/fullstack.code-workspace

/ui/ #ui project 
- /src/ #react code 
- /.vscode/launch.json #debugging config
- /... #other necessary files and folders
/data-access/
- /graphql/ #graph ql code 
- /.vscode/launch.json #debugging config
- /... #other necessary files and folders
```

### Create root directory

To begin, create a directory to house all of the code:

```bash
mkdir azure-quickstart
cd azure-quickstart
```

In the steps that follow, each of the folders noted in the overview will be created and populated.
