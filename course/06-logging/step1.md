
Creating a deployment for sinlge container pod

```
k create deployment nginx --iname=nginx
```{{exec}}

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





