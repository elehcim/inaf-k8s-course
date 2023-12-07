
The local-path-provisioner StorageClass has been installed, check with:

```
k get storageclass
```{{exec}}

<br>

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


<br>

create a Pod that uses this volume

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

<br>

status check

```
k get pod,pvc
```{{exec}}

<br>

Identify the IP address of the pod that exposes the web server port

```
k get pod -o wide
```{{exec}}

check the status of the web server

```
curl http://$IPPOD
```

> NOTE: The curl returns HTTP error code 403 because we don't have any files in the web root.
 

<br>

Delete pod

```
k delete pod pod-with-volume
```{{exec}}

<br>
File loading
<br>

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

<br>
Verifing..

```
k get pvc,storageclass
```{{exec}}

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

Create a pod with nginx and index.html exposed on port 80

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
      mountPath: /usr/share/nginx/html/
    ports:
    - containerPort: 80
  volumes:
  - name: my-vol
    persistentVolumeClaim:
      claimName: local-path-pvc
  initContainers:
  - name: install
    image: busybox
    volumeMounts:
    - mountPath: /usr/share/nginx/html/
      name: my-vol
    command:
    - wget
    - "-O"
    - "/usr/share/nginx/html/index.html"
    - https://raw.githubusercontent.com/elehcim/inaf-k8s-course/main/files/index.html
EOF
```{{exec}}

```
k delete pod pod-with-volume
```{{exec}}
