# Typesense Helm Charts

## Configuration values

| Property                      | Required| default             |
| :--------                     | :-----: | ------:             |
| typesense.apiKey              | true    |                     |
| typesense.apiPort             | true    | 8108                |
| typesense.peeringPort         | true    | 8107                |
| typesense.protocol            | true    | http                |
| typesense.cors.enabled        |         | true                |
| typesense.cors.domains        |         | ""                  |
| typesense.resetPeersOnError   |         | true                |
| storage.size                  | true    | 10Gi                |
| storage.storageClassName      | true    | default             |
| typesense.replicas            | true    | 3                   |
| scraper.schedule              | true    | '*/2 * * * *'       |
| scraper.config                | true    | *see values.yaml*   |
| dashboard.enabled             |         | true                |
| dashboard.replicas            |         | 1                   |


```yaml: values.yaml
typesense:
  apiKey: 
  apiPort: 8108
  peeringPort: 8107
  protocol: http
  cors:
    enabled: true
    domains: ""
  resetPeersOnError: true
  storage:
    size: 10Gi
    storageClassName: default
  replicas: 3
scraper:
  schedule: '*/2 * * * *'
  config: '{ "index_name": "typesense_docs", "start_urls": [], "selectors": {}, "scrape_start_urls": true,   "strip_chars": " .,;:#"}'
dashboard:
  enabled: true
  replicas: 1
```

## Deployment

```shell
helm repo add typesense-unofficial https://akyriako.github.io/typesense-helm
helm repo update

helm upgrade --install typesense typesense-unofficial/typesense \
    --set typesense.apiKey=<Admin API Key> 
    -n typesense \
    --create-namespace 
```