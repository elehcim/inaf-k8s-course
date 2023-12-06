
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
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-path-pvc2
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100Mi
EOF
```{{exec}}

> Volumes can only be as big as there is disk space on the nodes, keep them sizes low!


<br>

Create a Pod that uses the volume

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
    - name: my-vol2
      mountPath: /etc/nginx/conf.d/default.conf
    ports:
    - containerPort: 80
  - name: php
    image: my-php-app:1.0.0
    volumeMounts:
    - name: my-vol
      mountPath: /usr/share/nginx/html
  volumes:
  - name: my-vol
    persistentVolumeClaim:
      claimName: local-path-pvc
  - name: my-vol2
    persistentVolumeClaim:
      claimName: local-path-pvc2
  
EOF
```{{exec}}

<br>

Finally verify the status

```
k get pod,pv,pvc
```{{exec}}
