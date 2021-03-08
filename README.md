# Loki in Kind

1. Download the latest version of Loki (`0.10.0` as of writing this):
```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.10.0/kind-linux-amd64
chmod +x ./kind
mv ./kind /usr/local/bin
```

2. Change directory to `kind/`.

3. Create a kind cluster (`v1.19.7` in this case), using the provided cluster configuration:
```
kind create cluster --name loki-k8s --config k8s-1.19.7.yaml
```

4. Install the Nginx Ingress controller for Kind:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
```

5. Install Loki (not using the stack helm chart in this case):
```
helm repo add grafana https://grafana.github.io/helm-charts
helm upgrade --install loki --create-namespace --namespace=loki grafana/loki
```

6. _Skip this step if you want to use the provided file (called `auth`) in the next step._ Create the username and password file, username here is `loki` and the password is `secret`. The resulting file is called `auth`:
```
htpasswd -c auth loki
New password: secret
Re-type new password: secret
Adding password for user loki
```

7. Create a Secret using the `auth` file:
```
kubectl create secret generic -n loki basic-auth --from-file=auth
```

8. Apply the Ingress object:
``` 
kubectl apply -f nginx-ingress.yaml
```

9. Using `curl` try to reach Loki via the Nginx Ingress without Basic Auth, should return a `401`:
```
curl -vv -H 'Host: loki.local' http://localhost/api/prom/label
```

10. Using `curl` try to reach Loki via the Nginx Ingress using Basic Auth, should return `{"values":["__name__"]}`:
```
curl -vv -H 'Host: loki.local' http://localhost/api/prom/label
```
