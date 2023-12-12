
Create a pod with nginx and index.html exposed on port 80

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-html
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

Check exposed port

```
k get pod -o wide
```{{exec}}

```
export POD_IP=$(k get pod/pod-with-volume -o jsonpath='{.status.podIP}')
echo $POD_IP
```{{execute}}

check the status of the web server

```
curl http://$POD_IP
```{{execute}}

this is the result

```
k delete pod pod-with-html
```{{exec}}
