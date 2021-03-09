# Running a Nginx Ingress in-front of Loki with Basic Authentication

1. Install the Nginx Ingress controller for Kind:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
```

2. Install Loki (not using the stack helm chart in this case):
```
helm repo add grafana https://grafana.github.io/helm-charts
helm upgrade --install loki --create-namespace --namespace=loki grafana/loki
```

3. _Skip this step if you want to use the provided file (called `auth`) in the next step._ Create the username and password file, username here is `loki` and the password is `secret`. The resulting file is called `auth`:
```
htpasswd -c auth loki
New password: secret
Re-type new password: secret
Adding password for user loki
```

4. Create a Secret using the `auth` file:
```
kubectl create secret generic -n loki basic-auth --from-file=auth
```

5. Apply the Ingress object:
``` 
kubectl apply -f nginx-ingress.yaml
```

6. Using `curl` try to reach Loki via the Nginx Ingress without Basic Auth, should return a `401`:
```
curl -vv -H 'Host: loki.local' http://localhost/api/prom/label
```

7. Using `curl` try to reach Loki via the Nginx Ingress using Basic Auth, should return `{"values":["__name__"]}`:
```
curl -vv -H 'Host: loki.local' -u loki:secret http://localhost/api/prom/label
```

## Links:
* https://kubernetes.github.io/ingress-nginx/examples/auth/basic/
* https://github.com/grafana/helm-charts/tree/main/charts/loki
* https://grafana.com/docs/loki/latest/installation/helm/