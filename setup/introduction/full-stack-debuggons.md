---
description: (~1 minute) From a single VSCode window you can run the entire project.
---

# Full Stack Debugging

Add the following file to the `azure-quickstart` directory which will allow for the entire project to be debugged via single instance of VS Code.

{% code title="azure-quickstart/fullstack.code-workspace" %}
```
{
	"folders": [
		{
			"path": "ui"
		},
		{
			"path": "data-access"
		}
	],
	"settings": {
		"debug.internalConsoleOptions": "neverOpen"
	},
	"launch": {
		"version": "0.2.0",
		"compounds": [{
			"name": "full-stack",
			"configurations": [
			  {
				"folder": "ui",
				"name": "Launch Chrome against localhost"
			  },
			  {
				"folder": "data-access",
				"name": "Attach to Node Functions"
			  }
			]
		}],
	}
}
```
{% endcode %}

Once you've created this file you should be able to open it from VS Code and can debug both projects by selecting "full-stack" from the debug menu.
