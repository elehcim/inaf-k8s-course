## Using `kubectl describe` command

The `kubectl describe` command is used to get detailed information about Kubernetes resources.
When used with a pod, it provides information about the pod's current state, events, and related objects.

To describe a pod, you can use the following command:
```
kubectl describe pod my-first-pod
```{{execute}}

## Explore manifests

For example to get the ip of a pod you can use the following command:
```
kubectl get pod my-first-pod -o jsonpath='{.status.podIP}'
```{{execute}}
