`kubectl` (aliased to `k`) is the command line tool used to interact with the cluster.

To see the help page:
```
k --help
```{{execute}}

The main purpose of `kubectl` is, in fact, to interact with the API server of the cluster.
The API server is the main management unit of the cluster, and is responsible for all the operations that happen in the cluster.
Let's check the cluster status:

Run `kubectl cluster-info`{{exec}}

The output shows the endpoints of the API server and the DNS name of the cluster.

The main `kubectl` commands are:
- `kubectl get`: list resources
- `kubectl describe`: show detailed information about a resource
- `kubectl create`: create a resource
- `kubectl delete`: delete a resource
- `kubectl apply`: apply a configuration file to the cluster
- `kubectl exec`: execute a command in a container
- `kubectl logs`: show the logs of a container
