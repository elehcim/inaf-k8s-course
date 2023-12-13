
Let's repeat the same example, but using a Deployment.


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
---
apiVersion: v1 
kind: Service 
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


create a Deployment that uses this volume and this NodePort


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
          - mountPath: /usr/share/nginx/html/
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


```
k get pods
```{{exec}}

<br>

Try to access to your service vie HTTP<br>
Click on icon near minute left on the top right of the screen and click on the Traffic / Ports <br>
wrtie port 30000 in Custom Ports end click Access.

OR click here <br>
[ACCESS NGINX]({{TRAFFIC_HOST1_30000}})

<br>
Scale your eniroment

```
k scale deployment nginx --replicas=4
```{{exec}}

Check the Pods

```
k get pods
```{{exec}}

Scale to normal value

```
k scale deployment nginx --replicas=2
```{{exec}}


Clean all

```
k delete deployment nginx

```{{exec}}

```
k delete svc nginx
```{{exec}}



