It is normal to have multiple namespaces in the same cluster, so that it is easier to organize the apps running there.

Let's see which namespaces are available in the cluster:

```k get namespaces```{{execute}}

Let's create a new namespace:

```k create namespace my-namespace```{{execute}}

```k get namespaces```{{execute}}

To change the current namespace, run `k config set-context --current --namespace=<namespace-name>`{{execute}}.

Let's change the current namespace to `my-namespace`:

```k config set-context --current --namespace=my-namespace```{{execute}}

```k get namespaces```{{execute}}

