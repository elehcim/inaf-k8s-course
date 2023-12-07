
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
Verifica del caricamento

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
k delete pod pod-with-volume
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

