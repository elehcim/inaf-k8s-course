
Adding service NodePort to Pod

```
k get svc
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

Create a Service NodePort

```
cat <<EOF | kubectl apply -f -
kind: Service 
apiVersion: v1 
metadata:
  name: nginx
  labels:
    app: nginx
    layer: frontend
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - nodePort: 3000
      port: 80
      targetPort: 80
EOF
```{{exec}}


create a Pod that uses this volume and this NodePort


```
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
    layer: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
          - containerPort: 80
        volumeMounts:
          - mountPath: /user/share/nginx/html/
            name: contents
      initContainers:
      - name: install
        image: busybox
        volumeMounts:
          - mountPath: /usr/share/nginx/html/
            name: contents
        command:
        - wget
        - "-O"
        - "/usr/share/nginx/html/index.html"
        - https://raw.githubusercontent.com/elehcim/inaf-k8s-course/main/files/index.html
                
      volumes:
        - name: contents
          persistentVolumeClaim:
            claimName: local-path-pvc
EOF                
```{{exec}}


cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
    layer: frontend
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

<br>


