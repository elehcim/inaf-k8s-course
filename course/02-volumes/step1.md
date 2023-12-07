
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

> ATTENZIONE: non fare volumi superiori a 100Mi!!


<br>

Creare un pod che usa questo volume

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

Verifica dello stato

```
k get pod,pvc
```{{exec}}

<br>

Identificare l'indirizzo ip del pod che espone la porta del server web

```
k get pod -o wide
```{{exec}}

Verifica dello stato del server web

```
curl http://$IPPOD
```

> NOTA: la curl restituisce il codice di errore HTTP 403 perchè non abbiamo nessun file nello spazio web
 

<br>

Cancellare il pod

```
k delete pod pod-with-volume
```

<br>
Caricare i file nel volume
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
  volumes:
  - name: my-vol
    persistentVolumeClaim:
      claimName: local-path-pvc
  initContainers:
  - name: install
    image: busybox
    volumeMounts:
    - mountPath: /data/
      name: my-vol
    command:
    - tar
    - "-xvf"
    - "/data/"
    - https://raw.githubusercontent.com/elehcim/inaf-k8s-course/main/files/data.tar
EOF
```{{exec}}


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

