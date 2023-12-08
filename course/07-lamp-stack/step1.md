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

Add ConfigMap for apache2

```
k create configmap httpdconfig --from-file=https://

```
helm install k8s-apache --set imagePullPolicy=Always bitnami/apache --set htdocsPVC=local-path-pvc --set httpdConfigMap=httpdconfig
```{{exec}}







