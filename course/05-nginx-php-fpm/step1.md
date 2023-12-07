
Create your nginx php-fpm env

```
k get svc
```{{exec}}

<br>

Create a PVC (the default StorageClass will be used),
one for nginx configuration file and one for nginx contents

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

```
cat <<EOF | kubectl apply -f -
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
    - nodePort: 30000
      port: 80
      targetPort: 80
EOF
```{{exec}}

<br>
Create Cluster ip for php-fpm

```
cat <<EOF | kubectl apply -f -
kind: Service 
apiVersion: v1 
metadata:
  name: phpfpm
  labels:
    app: phpfpm
    layer: backend
spec:
  type: ClusterIP
  selector:
    app: phpfpm
  ports:
    - port: 9000
      targetPort: 9000
EOF
```{{exec}}

<br>
Create Service phpfpm

```
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: phpfpm
  labels:
    app: phpfpm
    layer: backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: phpfpm
  template:
    metadata:
      labels:
        app: phpfpm
    spec:
      containers:
        - name: phpfpm
          image: php:fpm-alpine
          ports:
            - containerPort: 9000
          volumeMounts:
            - mountPath: /var/www/html/
              name: contents
      volumes:
        - name: contents
          persistentVolumeClaim:
            claimName: local-path-pvc
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
          - mountPath: /var/www/html/
            name: contents
                
          - name: nginx-config
            mountPath: /etc/nginx/conf.d/default.conf
            subPath: default.conf
      initContainers:
      - name: install
        image: busybox
        volumeMounts:
          - mountPath: /var/www/html/
            name: contents
        command:
        - wget
        - "-O"
        - "/var/www/html/index.php"
        - https://raw.githubusercontent.com/elehcim/inaf-k8s-course/main/files/index.php
      - name: install2
        image: busybox
        volumeMounts:
          - name: nginx-config
            mountPath: /etc/nginx/conf.d/
        command:
        - wget
        - "-O"
        - "/etc/nginx/conf.d/default.conf"
        - https://raw.githubusercontent.com/elehcim/inaf-k8s-course/main/files/nginx.conf
                
      volumes:
        - name: contents
          persistentVolumeClaim:
            claimName: local-path-pvc
                
        - name: nginx-config
          persistentVolumeClaim:
            claimName: local-path-pvc2
EOF
```{{exec}}


```
k get pods -o wide
```{{exec}}

<br>

Try to access to your service vie HTTP<br>
Click on icon near minute left on the top right of the screen and click on the Traffic / Ports <br>
wrtie port 30000 in Custom Ports end click Access.

<br>
Scale your eniroment

```
k scale deployment nginx phpfpm --replicas=4
```{{exec}}

Check the Pods

```
k get pods
```{{exec}}

Scale to normal value

```
k scale deployment nginx phpfpm --replicas=2
```{{exec}}


Clean all

```
k delete deployment nginx phpfpm
```{{exec}}

```
k delete svc nginx phpfpm
```{{exec}}
