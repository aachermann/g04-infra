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

### Namespace

```bash
kubectl create namespace monitoring
```

### Sync ArgoCD