## Welcome !

In this scenario we'll learn how to launch and interact with the pods in the cluster.

**HAVE FUN**

Pods are the smallest deployable units of computing that can be created and managed in Kubernetes.
They run one or more containers on a node, and they are the building blocks of the cluster.

Pods are created and managed by the Kubernetes API server component, which is the control plane of the cluster.
The API server is the main management unit of the cluster, and is responsible for all the operations that happen in the cluster.

There are two ways to create pods in Kubernetes:

### Imperative approach
Launch a pod with the following specifications:

```
kubectl run -it my-first-pod --image=busybox -- sh
```{{exec}}

This created a pod called `my-first-pod` with a single container running the `busybox` image.

You can interact with this container and run commands in it. For example:
```
touch /tmp/foo.bar
```{{execute}}


Exit the shell with `exit`{{execute}}.
You can check the status of the pod with the following command:

```
kubectl get pods
```{{exec}}

You can reattach to the container with:
```
kubectl attach my-first-pod -c my-first-pod -i -t
```{{execute}}

```
ls /tmp
```{{execute}}

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
  image: busybox
EOF
```{{exec}}

Start a shell in the newly created pod:
```
kubectl exec -it my-first-pod-from-manifest -- sh
```{{exec}}
