Create this values file:

`values.yaml`

```yaml
rootUser: rootuser
rootPassword: rootpass123
```

```bash
helm repo add minio https://charts.min.io/
helm repo update
```{{exec}}

```bash
helm install minio minio/minio -f values.yaml
```{{exec}}