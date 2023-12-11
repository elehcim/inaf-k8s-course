### Declarative approach
You can use a manifest file to declare the desired state of the pod, and then apply it to the cluster.

Declare a pod with the following specifications:
```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: my-first-pod-from-manifest
spec:
  containers:
  - name: my-first-pod-from-manifest
    image: busybox
EOF
```{{exec}}

```
kubectl get pods
```{{exec}}

Start a shell in the newly created pod:
```
kubectl exec -it my-first-pod-from-manifest -- sh
```{{exec}}
