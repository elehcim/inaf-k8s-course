Deploy LAMP with HELM


```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-path-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100Mi
EOF
```{{exec}}

```
helm repo add bitnami https://charts.bitnami.com/bitnami
```{{exec}}

```
helm install k8s-apache --set imagePullPolicy=Always bitnami/apache --set htdocsPVC=local-path-pvc
```{{exec}}







