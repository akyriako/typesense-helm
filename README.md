# Typesense Helm Charts

[Typesense](https://typesense.org/) is an open-source search engine, alternative to [Algolia](https://www.algolia.com/), known for its speed and typo-tolerance, providing a fast and user-friendly search experience. It supports real-time indexing, faceted search, and full-text search, making it ideal for applications needing robust search capabilities. With simple APIs and SDKs for easy integration, Typesense is also optimized for performance with large datasets. It supports multiple languages and can be set up in a clustered environment for high availability and scalability.

> [!NOTE]
> This an **unofficial** Helm Chart.

## Components

The following components will be installed using this Helm Chart:

### Typesense

* Every`Pod` will be installed with two containers (default `replica` count is set to `3`). Typesense itself (version 26.0) and the Typesense Peer Resolver as a sidecar--see below.
* a headless `Service` (stems from the fact that Typesense needs to be installed as `StatefulSet`).
* A number of `PersistentVolumeClaim` and `PersistentVolume` objects; their number match the replica count.

### Typesense Peer Resolver

### DocSearch Scraper

### Typesense Dashboard



> [!IMPORTANT]
> This Helm Chart is unopinionated on how you are going to expose *Typesense* or *Typesense Dashboard* services, 
> and that's why **no** LoadBalancer or Ingress solution is provided. Nevertheless, **it is strongly recommended not exposing** 
> Typesense service out of the cluster as is, but use a reverse proxy instead. {Here]() you can find an example.  

## Deployment

### Requirements

### Parameters

| Property                      | Required| default                             |
| :--------                     | :-----: | ------:                             |
| typesense.apiKey              | true    |                                     |
| typesense.apiPort             | true    | 8108                                |
| typesense.peeringPort         | true    | 8107                                |
| typesense.protocol            | true    | http                                |
| typesense.cors.enabled        |         | true                                |
| typesense.cors.domains        |         | ""                                  |
| typesense.resetPeersOnError   |         | true                                |
| storage.size                  | true    | 10Gi                                |
| storage.storageClassName      | true    | default                             |
| typesense.replicas            | true    | 3                                   |
| scraper.enabled               |         | false                               |
| scraper.schedule              | true    | '*/2 * * * *'                       |
| scraper.config                | true    | *see charts/typesense/values.yaml*  |
| dashboard.enabled             |         | true                                |
| dashboard.replicas            |         | 1                                   |

### Installing the chart

```shell
helm repo add typesense-unofficial https://akyriako.github.io/typesense-helm
helm repo update

helm upgrade --install $RELEASE_NAME typesense-unofficial/typesense \
    --set typesense.apiKey=$ADMIN_API_KEY \
    -n $NAMESPACE \
    --create-namespace 
```

> [!TIP]
> You can easily create a random admin API key with the following command:
>
> ```shell
> sudo apt install rand -y
> export ADMIN_API_KEY=$(openssl rand -base64 14)
> ```

### Uninstalling the chart

```shell
helm uninstall $RELEASE_NAME -n $NAMESPACE
```
