The configuration file of the cluster is located in `~/.kube/config`.

```
cat ~/.kube/config
```{{execute}}

It shows the clusters, users and contexts available.
A more user-friendly way to see the configuration is:
```
k config view
```{{execute}} 

A kubernetes context is a group of access parameters to your cluster, including:
- a Kubernetes cluster (`kubernetes`)
- a user (`kubernetes-admin`)
- a namespace (optional, if not specified the 'default' namespace is used). 

The current context is `kubernetes-admin@kubernetes`.

Run `k config get-contexts`{{exec}}
