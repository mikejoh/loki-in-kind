# Loki in Kind

Various proof-of-concepts running Loki and related components in Kind.

## Pre-requisites

1. Download the latest version of [`kind`](https://kind.sigs.k8s.io/docs/user/quick-start/#installation) (`0.10.0` as of writing this):
```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.10.0/kind-linux-amd64
chmod +x ./kind
mv ./kind /usr/local/bin
```

2. Create a `kind` cluster (`v1.19.7` in this case), using the provided cluster configuration:
```
kind create cluster --name loki-k8s --config k8s-1.19.7.yaml
```

3. Install [`helm`](https://helm.sh/docs/intro/install/).