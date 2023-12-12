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
    command: ["/bin/sh", "-c", "while true; do echo \"hello from $(hostname)\"; sleep 10; done"]
EOF
```{{exec}}

> NOTE: we had to add a command to the container to keep it running, otherwise it would have exited immediately.

```
kubectl get pods
```{{exec}}

Start a shell in the newly created pod:
```
kubectl exec -it my-first-pod-from-manifest -- sh
```{{exec}}

> NOTE: the `kubectl exec` command is used to run a command in a container of a pod. The `--` is used to separate the `kubectl` command from the command to run in the container.
