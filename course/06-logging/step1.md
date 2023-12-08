
Creating a deployment for sinlge container pod

```
k create deployment nginx --image=nginx
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
cat <<EOF | kubectl apply -f -
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
EOF
```{{exec}}

Create a var that contain the name of the pod

```
PODNAME=$(kubectl get pods -l app=loggingdemo -o jsonpath='{ .items[0].metadata.name }')
echo $PODNAME
```{{exec}}

```
k logs $PODNAME
```{{exec}}

Lets get the logs from multi-container pod.... the will trhow an error dan ask us to define wich container

```
k logs $PODNAME -c container1
```{{exec}}

```
k logs $PODNAME -c container2
```{{exec}}

If we need to follow a log, we can do that...helpful in debugging real time issues This works for both
single and multi-container pods

```
k logs $PODNAME -c container1 --follow
```{{exec}}

CTRL+C to escape

If you have a lot of pod you can select only pods you want by apply a selector

```
k get pods --selector app=loggingdemo
```{{exec}}

Put your logs into a file

```
kubectl logs --selector app=loggingdemo --all-containers > allpods.txt
```{{exec}}

```
cat allpods.txt
```{{exec}}





