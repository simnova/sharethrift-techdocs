# Incorporate into Yaml

Add both of the



```
    - task: SonarCloudAnalyze@1
    - task: SonarCloudPublish@1
      inputs:
        pollingTimeoutSec: '300'   
```
