### Data pipeline jobs

Let's say we have a data processing pipeline, and we want to run it in the cluster.
We have a script that performs the data processing, and we want to run it multiple times in parallel, each time with a different input file.
The output of each run should be stored in a persistent storage.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: data-processing-job
spec:
  parallelism: 3  # Set the number of parallel pods to run for the job
  completions: 10  # Set the total number of pods to create for the job
  completionMode: Indexed
  template:
    spec:
      restartPolicy: Never
      containers:
      - name: data-processing-container
        image: python
        command: ["python"]
        args: ["/scripts/data_processing_script.py", "$(JOB_COMPLETION_INDEX)"]  # Pass the job index as an argument to the processing script
        volumeMounts:
        - name: input-volume
          mountPath: /input
        - name: output-volume
          mountPath: /output
        - name: my-script
          mountPath: /scripts
      volumes:
      - name: input-volume
        persistentVolumeClaim:
          claimName: input-pvc
      - name: output-volume
        persistentVolumeClaim:
          claimName: output-pvc
      - name: my-script
        configMap:
          name: my-script
  backoffLimit: 4  # The backoffLimit specifies the maximum number of retries before considering a Job as failed
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: input-pvc
spec:
  accessModes:
    - ReadWriteOnce # The appropriate model would be a ReadOnlyMany, but unfortunately "local-path": NodePath only supports ReadWriteOnce
  resources:
    requests:
      storage: 100Mi
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: output-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100Mi
---
```{{copy}}

Note on PVC access modes:

In Kubernetes, a PersistentVolumeClaim (PVC) can have different access modes that determine how the volume can be accessed from a pod.
Here are the three access modes:

- `ReadWriteOnce (RWO)`: This mode allows the volume to be mounted as read-write by a single node.
- `ReadOnlyMany (ROX)`: This mode allows the volume to be mounted as read-only by many nodes.
- `ReadWriteMany (RWX)`: This mode allows the volume to be mounted as read-write by many nodes.


```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-script
data:
  data_processing_script.py: |+
    import os
    print("Hello from data processing script!")
    print(f"JOB_COMPLETION_INDEX: {os.getenv("JOB_COMPLETION_INDEX")}")
```{{copy}}


Save all the above YAML in a file called `job.yaml`. Paste the config map below the job definition, we are using the yaml separator `---`

Apply the file with:

```
k apply -f job.yaml
```{{execute}}

Check the status of the job with:

```
k get job
```{{execute}}

So, a group of 10 pods have been created, each one running the data processing script with a different input.
To list the pods associated with the job, run:

```
k get pods --selector=job-name=data-processing-job
```{{execute}}

You can inspect the logs of the pods with:

```
k logs -l job-name=data-processing-job
```{{execute}}

> Note: the `-l` flag is used to filter the pods by label.

To cleanup the job, run:

```
k delete -f job.yaml
```{{execute}}