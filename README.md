
<!-- BEGIN MUNGE: UNVERSIONED_WARNING -->


<!-- END MUNGE: UNVERSIONED_WARNING -->

## This is fork from kubernets/examples/cassandra
https://github.com/kubernetes/kubernetes/tree/release-1.3/examples/cassandra


### Service uses type=NodePort to simulate load balancing


## Start clustered cassandra

```sh
# Start minikube
minikube start

# create a service to track all cassandra nodes
kubectl create -f cassandra-service.yaml

# create a replication controller to replicate cassandra nodes
kubectl create -f cassandra-controller.yaml
```

## Check that cluster started correct
```sh
# check names of created pods
kubectl get po

NAME              READY     STATUS      RESTARTS   AGE
cassandra-hr484   1/1       Completed   1          19h
cassandra-vt39a   1/1       Completed   1          19h


# validate the Cassandra cluster. Substitute the name of one of your pods.
kubectl exec -ti cassandra-hr484 -- nodetool status
```
## Connect to clustered cassandra
```sh
# check NodePort of service
kubectl describe service/cassandra

Name:     cassandra
Namespace:    default
Labels:     app=cassandra
Selector:   app=cassandra
Type:     NodePort
IP:     10.0.0.121
Port:     <unset> 9042/TCP
NodePort:   <unset> 32511/TCP
Endpoints:    172.17.0.3:9042,172.17.0.5:9042
Session Affinity: None
No events.

# check address of master host
kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443
KubeDNS is running at https://192.168.99.100:8443/api/v1/proxy/namespaces/kube-system/services/kube-dns
kubernetes-dashboard is running at https://192.168.99.100:8443/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard

# connect to cassandra's instances via master host
cqlsh 192.168.99.100 32511 --cqlversion="3.4.2"

# scale up the Cassandra cluster
kubectl scale rc cassandra --replicas=4
```
## Shutdown
```sh
# resource cleanup
kubectl delete service -l app=cassandra
kubectl delete rc cassandra

# Stop minikube
minikube stop
```
