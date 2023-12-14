## How to use persistent volumes in Kubernetes environment

Pods are ephemereal: data they store in their containers is lost when they are deleted.
Also, one fundating concept of kubernetes is that the controlplane is allowed to move pods around on the nodes in order to react to change of loads or to user requirements.

Kubernetes provides an abstraction to store data in a persistent way: the PersistentVolume.
The kubernetes administrator can create PersistentVolumes and users can ask for PersistentVolumeClaims in case they need to store data.
