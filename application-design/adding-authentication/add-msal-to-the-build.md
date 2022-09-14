# Add MSAL to the build



Go to Azure Pipelines and go to the UI and add a new Variables:

|  **Name**                            | Value                                                                           |
| ------------------------------------ | ------------------------------------------------------------------------------- |
| REACT\_APP\_FUNCTION\_ENDPOINT       | <p>&#x3C;FRONT DOOR URL> <br>(e.g.: https://sharethrift&#x3C;>.azurefd.net)</p> |
| REACT\_APP\_AAD\_APP\_CLIENTID       | <\<YOUR  AAD APP UI CLIENT ID>>                                                 |
| REACT\_APP\_AAD\_DIRECTORY\_TENANTID | <\<YOUR AAD TENANT ID>>                                                         |
| REACT\_APP\_AAD\_REDIRECT\_URI       | (YOUR CDN URL:) https://sharetrift<\<randomnumber>>.azureedge.net               |
| REACT\_APP\_AAD\_SCOPES              | api://sharethrift<>.com/access\_as\_user                                        |

Add these variable to the YML file

{% code title="/azure-quickstart/ui/azure-pipelines.yml" %}
```yaml
- script: |
    npm install
    npm run build
  displayName: 'npm install and build'
  workingDirectory: ui
  env:
    INLINE_RUNTIME_CHUNK: false 
    REACT_APP_FUNCTION_ENDPOINT: $(REACT_APP_FUNCTION_ENDPOINT) #ADD THIS AND THE LINES BELOW
    REACT_APP_AAD_APP_CLIENTID: $(REACT_APP_AAD_APP_CLIENTID) 
    REACT_APP_AAD_DIRECTORY_TENANTID: $(REACT_APP_AAD_DIRECTORY_TENANTID)
    REACT_APP_AAD_REDIRECT_URI: $(REACT_APP_AAD_REDIRECT_URI)
    REACT_APP_AAD_SCOPES: $(REACT_APP_AAD_SCOPES)
```
{% endcode %}

