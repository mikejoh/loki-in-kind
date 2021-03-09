# Testing Loki

1. Install Loki, promtail and Grafana (not using the stack helm chart in this case):
```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
kubectl create namespace loki
helm upgrade --install loki --namespace=loki grafana/loki
helm upgrade --install grafana --namespace=loki grafana/grafana
helm upgrade --install promtail --namespace=loki grafana/promtail \
    --set "loki.serviceName=loki.loki.svc.cluster.local" \
    --set "config.lokiAddress=http://loki.loki.svc.cluster.local:3100/loki/api/v1/push"
```

2. Get the `admin` password for Grafana:
```
kubectl get secret --namespace loki grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

3. Port-forward to Grafana:
```
kubectl port-forward --namespace loki service/grafana 3000:80
```

4. Login Grafana using the `admin` password fetched in step 2.

5. Add a new Datasource using the following endpoint: `http://loki.loki.svc.cluster.local:3100`

6. Add a new panel and add the following query:
```
{ namespace="kube-system" }
```