Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called namespaces.

It is normal to have multiple namespaces in the same cluster, so that it is easier to organize the apps running there.

Let's see which namespaces are available in the cluster:

```
k get namespaces
```{{execute}}

Let's create a new namespace:

```
k create namespace my-namespace
```{{execute}}

```
k get namespaces
```{{execute}}


To change the current namespace to the newly created one, run:
```
k config set-context --current --namespace=my-namespace
```{{execute}}

See the current configuration now, the namespace is specified explicitly instead of the default one:

```
k config view
```{{execute}} 

