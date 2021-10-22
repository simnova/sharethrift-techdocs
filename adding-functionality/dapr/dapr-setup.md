# DAPR setup



#### DAPR Items (coming soon - please skip for now)

*
  * Docker Desktop
  * [minikube](https://minikube.sigs.k8s.io/docs/start/)
    * `minikube version`
      * should return 1.16 or greater
    * `kubectl version`
      * should return a version supported by the AKS service
        * to determine [currently supported AKS versions](https://docs.microsoft.com/en-us/azure/aks/supported-kubernetes-versions), use the command
          * `az aks get-versions --location eastus2 --output table`
      * to start the cluster with a specific version use a command like:
        * `minikube start --kubernetes-version=v1.19.3`
  * [Helm](https://helm.sh/docs/intro/install/)
    * `helm version`
      * should return v3.4.2 or greater
  * [Install Redis using Helm](https://docs.dapr.io/getting-started/configure-state-pubsub/#tabs-3-kubernetes) (yaml files will be added later)
    *   `kubectl get pods`

        * should return:

        ```
        NAME             READY   STATUS    RESTARTS   AGE
        redis-master-0   1/1     Running   1          17h
        redis-slave-0    1/1     Running   1          17h
        redis-slave-1    1/1     Running   1          17h
        ```
  * [Install Dapr using Helm](https://docs.dapr.io/getting-started/install-dapr-kubernetes/#add-and-install-dapr-helm-chart)
    * export PATH=$PATH:\~/.dapr/bin
      *   `kubectl get pods -n dapr-system -w`

          * should return:

          ```
          NAME                                    READY   STATUS    RESTARTS   AGE
          dapr-dashboard-7bc49bc56f-6dw5r         1/1     Running   1          17h
          dapr-operator-7c7f8b57fb-ptmvj          1/1     Running   3          17h
          dapr-placement-86df45cc96-2cdwj         1/1     Running   2          17h
          dapr-sentry-6684f48fc9-4mhd9            1/1     Running   2          17h
          dapr-sidecar-injector-55c5fbbb9-4zhl6   1/1     Running   2          17h
          ```
*
