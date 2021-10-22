---
description: (~5 minutes)
---

# Creating GitHub Project

### Remove Cached repo for UI

The UI project may still have GIT repo associated with it, we'll forceably remove it:

```
cd ui
git rm -r --cached . -f
rm -rf .git
cd ../
```

### Initializing the local repo

Return back to the azure-quickstart directory you had created earlier, this directory contains /ui, /data-access and /fullstack.code-workspace

In a command line type:&#x20;

{% code title="azure-quickstart/" %}
```
git init .

```
{% endcode %}

### Add Files to Local Repo (we're also renaming master to main)

```
git add .
git commit -m "initial commit"
git branch -m master main
```

### Add Remote

Go to Github and create a new project, and then we'll add a remote:

```
git remote add origin <<remote url>>
```

### Push to GitHub

\*Note: You may need to do this step from VSCode

```
git push origin main
```
