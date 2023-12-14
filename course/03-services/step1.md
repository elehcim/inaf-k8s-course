# Adding service NodePort to Pod

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

Create a Service of type NodePort

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

```
k get svc -o wide
```{{exec}}

The `selector` is the key point here: from this you link the service to the pods.

### Create a Pod that uses this volume and this NodePort

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx  # Label used by service to select the pod
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

```
k get pod -o wide
```{{exec}}


Try to access to your service vie HTTP<br>
Click on icon near minute left on the top right of the screen and click on the Traffic / Ports <br>
wrtie port 30000 in Custom Ports end click Access.
<br>

OR click here <br>
[ACCESS NGINX]({{TRAFFIC_HOST1_30000}})

Clean all

```
k delete svc nginx
```{{exec}}

```
k delete pod nginx
```{{exec}}

