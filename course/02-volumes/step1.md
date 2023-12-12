In Killercoda the local-path-provisioner StorageClass is used to create PersistentVolumes on the fly.
A storage class provides a way for administrators to describe the "classes" of storage they offer.
Different classes might map to quality-of-service levels, or to backup policies, or to arbitrary policies determined by the cluster administrators.

```
k get storageclass
```{{exec}}

### Create a PersistentVolumeClaim (PVC)
Create a PVC (the default StorageClass will be used):

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-path-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100Mi
EOF
```{{exec}}

> ATTENTION: do not create volumes larger than 100M!!

Check the status of the PV and PVC with:
```
k get pv,pvc
```{{exec}}

### Let the pod mount the PVC

Create a Pod that uses this volume

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-volume
spec:
  containers:
  - name: container
    image: nginx:stable-alpine
    volumeMounts:
    - name: my-vol
      mountPath: /usr/share/nginx/html
    ports:
    - containerPort: 80
  volumes:
  - name: my-vol
    persistentVolumeClaim:
      claimName: local-path-pvc
EOF
```{{exec}}


Check the status with:

```
k get pod,pvc
```{{exec}}


Identify the IP address of the pod that exposes the web server port

```
k get pod -o wide
```{{exec}}

To save the IP address of the pod in a variable:

```
export POD_IP=$(k get pod/pod-with-volume -o jsonpath='{.items[0].status.podIP}')
```{{execute}}

check the status of the web server

```
curl http://$POD_IP
```

> NOTE: The curl returns HTTP error code 403 because we don't have any files in the web root.
 

<br>

Delete pod

```
k delete pod pod-with-volume
```{{exec}}

### File loading
We'll use the initContainers to download and uncompress a file in the volume.
InitContainers are containers that run before the main container starts.
They are used to perform operations that are needed before the main container starts.

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-volume
spec:
  containers:
  - name: container
    image: busybox
    volumeMounts:
    - name: my-vol
      mountPath: /data/
    command: ['sh', '-c', 'sleep infinity']
  volumes:
  - name: my-vol
    persistentVolumeClaim:
      claimName: local-path-pvc
  initContainers:
  - name: download
    image: busybox
    volumeMounts:
    - mountPath: /data/
      name: my-vol
    command:
    - wget
    - "-O"
    - "/data/data.tar"
    - https://raw.githubusercontent.com/elehcim/inaf-k8s-course/main/files/data.tar
  - name: uncompress
    image: busybox
    volumeMounts:
    - mountPath: /data/
      name: my-vol
    command: ['sh', '-c', 'tar -xvf /data/data.tar -C /data/']
EOF
```{{exec}}

Verify

```
k get pvc,storageclass
```{{exec}}

Get into the pod
```
k exec -it pod-with-volume -- sh
```{{exec}}

```
ls -la /data
```{{exec}}

```
exit
```{{exec}}

```
k delete pod pod-with-volume
```{{exec}}
