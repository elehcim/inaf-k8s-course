
<br>

## Welcome !

In this scenario we'll learn how to launch and interact with the pods in the cluster.

**HAVE FUN**

### Declarative approach

#### Task 1: Launch a pod
Declare a pod with the following specifications:

```
kubectl run -it my-first-pod --image=busybox -- sh
```{{exec}}

```
kubectl get pods
```{{exec}}

#### Task 2: Interact with the pod


### Imperative approach

#### Task 1: Launch a pod

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: my-first-pod-manifest
spec:
  image: busybox
EOF
```{{exec}}

Start a shell in the newly created pod:
```
kubectl exec -it my-first-pod-manifest -- sh
```{{exec}}
