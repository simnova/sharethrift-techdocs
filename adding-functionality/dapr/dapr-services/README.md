# DAPR Services (ignore for now)

## Configure local DAPR State and Messaging

Create a `deploy` directory in the `azure-quickstart` directory to house yaml files.

Create two files `redis-state.yaml `and `redis-pubsub.yaml ` in the `deploy` directory and configure according to [Dapr's instructions](https://docs.dapr.io/getting-started/configure-state-pubsub/#create-state-store-component), these will be used for local state and messaging, when deploying to production azure services will be leveraged instead. _One benefit of DAPR is allowing you to easily swap out infrastructure components._

Apply the configuration:

```
kubectl apply -f deploy/redis-state.yaml
kubectl apply -f deploy/redis-pubsub.yaml
```

## &#x20;Additional Services

Create four additional directories in the `azure-quickstart` directory for kubernetes services:

```
/azure-quickstart
    /identity
    /search
    /sharable
    /content-moderation
```

### Identity

Create an instance of the terminal and  navigate to the `/azure-quickstart/identity `directory&#x20;

Initialize a new project using `npm init` command and accept all the defaults.

Add additional NPM modules to set up basic services: `npm i express express-validator`

Create an

s



### Search

### Sharable

### Content Moderation





To start the mininkube dashboard in a browser window run:

`minikube dashboard`

*
