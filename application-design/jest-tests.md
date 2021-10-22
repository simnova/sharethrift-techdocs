---
description: Data Access Project
---

# Jest Tests

Data Access Project



Initialize Jest by running these commands

{% code title="terminal : azure-quickstart/data-access" %}
```bash
npm i -D jest typescript
npm i -D ts-jest @types/jest
npx ts-jest config:init
```
{% endcode %}

Confgure jest config

{% code title="azure-quickstart/data-access/jest.config.js" %}
```bash
module.exports = {
  roots: ["<rootDir>/."],
  preset: 'ts-jest',
  testEnvironment: 'node',
  testMatch: ['**/**/*.test.ts'],
    coverageReporters: ['json', 'lcov'],
    coverageDirectory: 'coverage',
    collectCoverageFrom: [
      "**/*.{js,jsx,ts}",
      "!**/node_modules/**",
      "!**/dist/**"
    ]
};
```
{% endcode %}



Configure package.json

{% code title="azure-quickstart/data-access/package.json" %}
```bash
...
  "scripts": {
    "test:ci": "npx jest --watchAll=false --coverage=true",
    "test": "npx jest --watch"
  },

...
```
{% endcode %}



Configure yaml

{% code title="azure-quickstart/data-access/azure-pipelines.yml" %}
```yaml
>> UPDATE NPM BUILD TASK
>> Add 'npm run test:ci' before build production
...
    # Build project and run tests
    - task: Bash@3
      displayName: 'NPM: Prepare binaries and run tests'
      inputs:
        targetType: 'inline'
        script: |
          export NODE_OPTIONS=--max_old_space_size=16384
          npm ci
          npm run test:ci
          npm run build:production
        workingDirectory: 'data-access'
...

>> UPDATE SONAR CLOUD PREPARE TASK
>> Add sonar.javascript.lcov.reportPaths to extraProperties
...
    # Set up SonarCloud
    - task: SonarCloudPrepare@1
      displayName: 'SonarCloud: Prepare analysis configuration'
      inputs:
        SonarCloud: 'sonarcloud'
        organization: 'simnova'
        scannerMode: 'CLI'
        configMode: 'manual'
        cliProjectKey: 'sharethrift-data-access'
        cliProjectName: 'sharethrift-data-access'
        cliSources: './data-access'
        extraProperties: |
          sonar.javascript.lcov.reportPaths=$(System.DefaultWorkingDirectory)/data-access/coverage/lcov.info
```
{% endcode %}

Configure .gitignore

{% code title=".gitignore" %}
```yaml
...
...
/coverage
```
{% endcode %}

References:

