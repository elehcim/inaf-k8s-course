Let's check how many nodes we have in the cluster:

Run `k get nodes -o wide`{{exec}}

We have one node which is the control plane node `controlplane`, and one worker node `node01`.

We can interact with the nodes, e.g. by pinging them:

```
ping controlplane
```{{exec}}