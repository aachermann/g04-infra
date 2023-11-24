## Installing Grafana and Loki

### Installing Helm
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
rm get_helm.sh
```

### Adding Helm-Repo

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

### Loki

```bash
helm template loki grafana/loki-stack --namespace monitoring > monitoring/loki.yaml
```

### Grafana
```bash
helm template grafana grafana/grafana --namespace monitoring > monitoring/grafana.yaml
```

### Prometheus Operator
```bash
LATEST=$(curl -s https://api.github.com/repos/prometheus-operator/prometheus-operator/releases/latest | jq -cr .tag_name)
curl -sL https://github.com/prometheus-operator/prometheus-operator/releases/download/${LATEST}/bundle.yaml | kubectl create -f -
kubectl wait --for=condition=Ready pods -l  app.kubernetes.io/name=prometheus-operator -n default

```



### Namespace

```bash
kubectl create namespace monitoring
```



### Sync ArgoCD

```bash
kubectl get secrets -n monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```
