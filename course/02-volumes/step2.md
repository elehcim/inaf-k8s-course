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
