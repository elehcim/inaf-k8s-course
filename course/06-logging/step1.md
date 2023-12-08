
Creating a deployment for sinlge container pod

```
k create deployment nginx --iname=nginx
```{{exec}}

Create a var that contain the name of the pod
```
PODNAME=$(k get pods -l app=nginx -o jsonpath='{ .items[0].metadata.name }')
```{{exec}}

```
echo $PODNAME
```{{exec}}

```
k logs $PODNAME
```{{exec}}

<br>

Cerate a multi-container pod tath write some information to stdout

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: loggingdemo
  labels:
    app: loggingdemo
spec:
  replicas: 3
  selector:
    matchLabels:
      app: loggingdemo
  template:
    metadata:
      labels:
        app: loggingdemo
    spec:
      containers:
      - name: container1
        image: busybox
        args: [/bin/sh, -c, 'while true; do echo "$(date)": $(hostname): container1; sleep 1; done']
      - name: container2
        image: busybox
        args: [/bin/sh, -c, 'while true; do echo "$(date)": $(hostname): container2; sleep 1; done']
```{{exec}}

Create a var that contain the name of the pod

```
PODNAME=$(kubectl get pods -l app=loggingdemo -o jsonpath='{ .items[0].metadata.name }')
echo $PODNAME
```{{exec}}

```
k logs $PODNAME
```{{exec}}

Let's get the logs from multi-container pod.... the will trhow an error dan ask us to define wich container

```
k logs $PODNAME -c container1
```{{exec}}

```
k logs $PODNAME -c container2
```{{exec}}

If we need to follow a log, we can do that...helpful in debugging real time issues This works for both
single and multi-container pods

```
k logs $PODNAME -c contanier1 --follow
ctrl+c
```{{exec}}



