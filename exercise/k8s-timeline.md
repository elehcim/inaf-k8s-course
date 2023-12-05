# Introduzione a K8S

## Cluster 

1- Informazioni sul cluster

	kubectl cluster-info

2- stato del cluster e dei nodi

	kubectl get nodes o wide

3- lista dei pods

	kubectl get pods

4- namespaces

	kubectl get namespaces

6- in quale namespace sono

	kubectl config get-contexts

5- cambiare namespace di default

	kubectl config set-context --current --namespace=inaf-container-school

## Pods

1- Avviare il primo pod
	
	kubectl run -it my-first-pod --image=busybox -- sh
	If you don't see a command prompt, try pressing enter.
	/ #
	/ # ping 8.8.8.8 
	PING 8.8.8.8 (8.8.8.8): 56 data bytes
	64 bytes from 8.8.8.8: seq=0 ttl=116 time=17.474 ms
	64 bytes from 8.8.8.8: seq=1 ttl=116 time=17.278 ms
	^C
	--- 8.8.8.8 ping statistics ---
	2 packets transmitted, 2 packets received, 0% packet loss
	round-trip min/avg/max = 17.278/17.376/17.474 ms
	/ # 

2- avviare il primo server web

	kubectl run my-first-web --image=nginx:alpine

3- quali pod sono avviati

	kubectl get pods
	NAME           READY   STATUS    RESTARTS   AGE   IP           NODE                                   NOMINATED NODE   READINESS GATES
	my-first-web   1/1     Running   0          6s    10.42.4.14   kubernetes-worker-02.oa-roma.inaf.it   <none>           <none>
	my-first-pod   1/1     Running   0          7s    10.42.3.12   kubernetes-worker-01.oa-roma.inaf.it   <none>           <none>

-	verifica del web server (comando da eseguire su un nodo k8s)
	
		curl http://10.42.4.14
	
4- cancellare il pod

	kubectl delete pod my-first-pod
	kubectl delete pod my-first-web

### Metodo Imperativo

