# Typesense Helm Charts

## Components

### Typesense

### Typesense Peer Resolver

### DocSearch Scraper

### Typesense Dashboard

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

### Uninstalling the chart

```shell
helm uninstall $RELEASE_NAME -n $NAMESPACE
```
