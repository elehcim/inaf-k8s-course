
There are two ways to create pods in Kubernetes:
- imperative approach: you tell the controlplane what to do
- declarative approach: you tell the controlplane what you want, and it will try to make it happen

### Imperative approach
Launch a pod with the following specifications:

```
kubectl run -it my-first-pod --image=busybox -- sh
```{{exec}}

This created a pod called `my-first-pod` with a single container running the `busybox` image.

You can interact with this container and run commands in it. For example:
```
hostname
```{{execute}}

Exit the shell with `exit`{{execute}}.
The container terminated and the pod is now in the `Completed` state.
After a little while the controlplane will see that the pod is not running anymore and will try to recreate it.

You can check the status of the pod with the following command:

```
kubectl get pods
```{{exec}}

Once the pod is running you can attach to the container with:
```
kubectl attach my-first-pod -c my-first-pod -i -t
```{{execute}}


Check the number of RESTARTS for the pod:
```
kubectl get pods
```{{exec}}

Delete the pod:
```
kubectl delete pod my-first-pod
```{{execute}}
