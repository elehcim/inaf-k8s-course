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
  template:
    spec:
      restartPolicy: Never
      initContainers:
      - name: 'format-input-data'
        image: 'docker.io/library/bash'
        command:
        - "bash"
        - "-c"
        - |
          items=(foo bar baz qux xyz)
          echo ${items[$JOB_COMPLETION_INDEX]} > /input/data.txt
        volumeMounts:
        - mountPath: /input
          name: input-volume
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
    - ReadOnlyMany
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
    - ReadWriteMany
  resources:
    requests:
      storage: 100Mi
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
    print("JOB_COMPLETION_INDEX: {}".format(os.environ["JOB_COMPLETION_INDEX"]))
    print("INPUT_FILE: {}".format(os.environ["INPUT_FILE"]))
    print("OUTPUT_FILE: {}".format(os.environ["OUTPUT_FILE"]))
    print("DATA: {}".format(open(os.environ["INPUT_FILE"]).read()))
    open(os.environ["OUTPUT_FILE"], "w").write("Hello from data processing script!")
```