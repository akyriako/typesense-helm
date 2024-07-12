# Typesense Helm Charts

## Configuration values

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
| scraper.schedule              | true    | '*/2 * * * *'                       |
| scraper.config                | true    | *see charts/typesense/values.yaml*  |
| dashboard.enabled             |         | true                                |
| dashboard.replicas            |         | 1                                   |


## Deployment

```shell
helm repo add typesense-unofficial https://akyriako.github.io/typesense-helm
helm repo update

helm upgrade --install typesense typesense-unofficial/typesense \
    --set typesense.apiKey=<Admin API Key> \
    -n typesense \
    --create-namespace 
```