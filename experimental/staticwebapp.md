# StaticWebApp

In Azure Portal&#x20;

Choose Build Presset: React

App location: /ui

Api location: /data-access

Output Location:build





Add staticwebapp.config.json file to the ui project



```
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": [
        "authenticated"
      ]
    },
    {
      "route": "/admin/*",
      "allowedRoles": [
        "administrator"
      ]
    },
    {
      "route": "/images/*",
      "headers": {
        "cache-control": "must-revalidate, max-age=15770000"
      }
    },
    {
      "route": "/api/*",
      "headers": {
        "Server": "",
        "X-Content-Type-Options": "nosniff",
        "X-Frame-Options": "SAMEORIGIN",
        "Access-Control-Allow-Credentials": "true",
        "Access-Control-Allow-Origin": "*",
        "Access-Control-Allow-Methods": "GET, POST, OPTIONS, PUT, HEAD, DELETE, PATCH",
        "Access-Control-Allow-Headers": "X-Requested-With, content-type, Authorization, X-Apollo-Tracing, Apollo-Query-Plan-Experimental",
        "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'; script-src 'self' https://cdn.jsdelivr.net"
      }
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": [
        "administrator",
        "customers_contoso"
      ]
    },
    {
      "route": "/loginxxx",
      "rewrite": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": 404
    },
    {
      "route": "/logoutxx",
      "redirect": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "rewrite": "/calendar.html"
    },
    {
      "route": "/specials",
      "redirect": "/deals",
      "statusCode": 301
    }
  ],
  "navigationFallback": {
    "rewrite": "index.html",
    "exclude": [
      "/images/*.{png,jpg,gif}",
      "/css/*"
    ]
  },
  "responseOverrides": {
    "400": {
      "rewrite": "/invalid-invitation-error.html"
    },
    "401": {
      "redirect": "/login",
      "statusCode": 302
    },
    "403": {
      "rewrite": "/custom-forbidden-page.html"
    },
    "404": {
      "rewrite": "/404.html"
    }
  },
  "globalHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'; script-src 'self' https://cdn.jsdelivr.net"
  },
  "mimeTypes": {
    ".json": "text/json"
  }
}
```

Edit build file  Add INLINE RUNTIME CHUNK

{% code title=".github/worflows/**some-random-name**.yml" %}
```
jobs:
  build_and_deploy_job:
    ..
    name: Build and Deploy Job
    steps:
      ..
      - name: Build And Deploy
        ..
        with:
          ..
        env:
          INLINE_RUNTIME_CHUNK: 'false'
```
{% endcode %}
