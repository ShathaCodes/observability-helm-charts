# Helm Chart to Deploy an Observability Stack

[![example workflow](https://gitlab.com/ShathaCodes/observability-helm-charts/badges/main/pipeline.svg)](https://gitlab.com/ShathaCodes/observability-helm-charts/-/pipelines/614373747)

## Deploy the Stack

```
kubectl create namespace monitoring
```

### Deploy Prometheus Operator

1. Create a secret for Grafana credentials

```
kubectl create secret generic grafana-admin-credentials --from-file=./admin-user --from-file=./admin-password -n monitoring
```

2. Add helm repo

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

3. Deploy

```
helm upgrade --install -n monitoring prometheus prometheus-community/kube-prometheus-stack -f kube-prometheus-stack\values.yaml
```


### Deploy Loki Stack : Loki + Promtail

1. Add helm repo

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

2. Deploy
```
helm upgrade --install loki grafana/loki-stack -n monitoring
```

### Deploy Tempo
```
helm upgrade --install tempo grafana/tempo -n monitoring -f tempo\values.yaml
```

### Deploy Prometheus Postgres Exporter/
1. Create secret for database password
```
kubectl create secret generic postgres-user-pass --from-file=./password -n monitoring
```

2. Deploy
```
helm upgrade --install postgres-exporter prometheus-community/prometheus-postgres-exporter -n monitoring -f postgres-exporter\values.yaml
```

## Access the Stack

### Access Grafana 
```
kubectl port-forward service/grafana 3000:80 -n monitoring
```
Go to ``localhost:3000``

### Access Prometheus 
```
kubectl port-forward service/prometheus-prometheus 9090:9090 -n monitoring
```
Go to ``localhost:9090``
