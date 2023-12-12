## Using `kubectl describe` command

The `kubectl describe` command is used to get detailed information about Kubernetes resources.
When used with a pod, it provides information about the pod's current state, events, and related objects.

To describe a pod, you can use the following command:
```
kubectl describe pod my-first-pod-from-manifest
```{{execute}}

## Explore manifests
You can get the manifest file kubernetes has stored with all the resource metadata with the following command:

```
kubectl get pod my-first-pod-from-manifest -o yaml'
```{{execute}}

You can also get single values from the manifest using the `jsonpath` format.
For example to get the ip of a pod you can use the following command:
```
kubectl get pod my-first-pod-from-manifest -o jsonpath='{.status.podIP}'
```{{execute}}