# Typesense Helm Charts

[Typesense](https://typesense.org/) is an open-source search engine, alternative to [Algolia](https://www.algolia.com/), known for its speed and typo-tolerance, providing a fast and user-friendly search experience. It supports real-time indexing, faceted search, and full-text search, making it ideal for applications needing robust search capabilities. With simple APIs and SDKs for easy integration, Typesense is also optimized for performance with large datasets. It supports multiple languages and can be set up in a clustered environment for high availability and scalability.

> [!NOTE]
> This an **unofficial** Helm Chart.

## Components

The following components will be installed using this Helm Chart:

### Typesense

* Every`Pod` will be installed with two containers (default `replica` count for the pods is set to `3`). Typesense itself (version `26.0`) and the Typesense Peer Resolver as a sidecar--see below.
* a headless `Service` (stems from the fact that Typesense needs to be installed as `StatefulSet`).
* A number of `PersistentVolumeClaim` and `PersistentVolume` objects; their number match the replica count.

### Typesense Peer Resolver for Kubernetes

Typesense Peer Resolver is a sidecar container for Typesense, that automatically resets the nodes peer list for HA Typesense clusters in Kubernetes by identifying the new endpoints of the headless service. When restarting/upgrading Typesense nodes in a high-availability cluster scenario running in Kubernetes, DNS entries of the `StatefulSet` do not get resolved again with the new IP, preventing the pods/peers to rejoin the cluster, even if you have the `TYPESENSE_RESET_PEERS_ON_ERROR` flag enabled. With this sidecar, we do not need to provide a **node list** to Typesense in order because the sidecar will create one on the fly and update the necessary file in the mount that shares with the Typesense container (remember they live in the same pod).

Typesense Peer Resolver will additionally install:

* a Kubernetes `ServiceAccount` 
* a Kubernetes `Role` that grants `get`, `list` and `watch` permission for `Endpoints`
* a Kubernetes `RoleBinding`

> [!NOTE]
> For more information on Typesense Peer Resolver check the [repository](https://github.com/akyriako/typesense-peer-resolver) of the project:

### DocSearch Scraper

The Helm Chart installs the customized version of DocSearch that works with Typesense as a `CronJob`. It will periodically crawl and scrape the site(s) defined in `start_urls` of the **config.json** configuration file of the scraper. An additional `ConfigMap` is installed, that holds the value of the **config.json** configuration file required from DocSearch Scraper. You can provide you own configuration via the `scraper.config` value of the Helm Chart. The value should be in `JSON` format, but passed as `string`.

> [!CAUTION]
> If you enable the scraper, it will provision a scaffold **config.json** in the accompanying `ConfigMap`, and every `Job` spawned from the `CronJob` will exit as `Failed`. **This doesn't mean that the DocSearch Scraper is not installed correctly**, it is absolutely normal; it is just missing the required parameters to crawl your target site(s). For more information how to create and configure your own config.json file can be found [here](https://typesense.org/docs/guide/docsearch.html#create-a-docsearch-scraper-config-file). 

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
