# gke-nfs-persistence-volume

Create a Network File System(NFS) Server with Google Compute Engine persistent disks and mount them to GKE containers.

Set variables

```bash
NAME=gke-cluster01
ZONE=us-central1-c
NUM_NODES=2
MACHINE_TYPE=n1-standard-1
```

Create GKE cluster

```bash
gcloud container clusters create $NAME \
--zone $ZONE \
--username "admin" \
--cluster-version "latest" \
--machine-type $MACHINE_TYPE \
--disk-type "pd-ssd" \
--disk-size "100" \
--num-nodes $NUM_NODES \
--enable-cloud-logging \
--enable-cloud-monitoring \
--network "default" \
--addons HorizontalPodAutoscaling,HttpLoadBalancing,KubernetesDashboard
```

Get cluster

```bash
gcloud container clusters get-credentials $NAME --zone $ZONE
```

Create Storage resources

```bash
kubectl apply -f config/storageclass.yaml
```

Create Volume

```bash
kubectl apply -f config/nfs-pvc.yaml
```

You can add the deployment and the service to your cluster with these commands:

```bash
kubectl apply -f config/nfs-deployment.yaml
kubectl apply -f config/nfs-service.yaml
```

Check pods

```bash
kubectl get pods
```

Check services

```bash
kubectl get svc
```

Connect to nfs to create dummy content

```bash
kubectl exec -it nfs-server-58fd574d69-pxsrq -- /bin/bash
```

Write content

```bash
cd /nfsshare
echo "<h1>Welcome to NFS</h1>" > index.html
exit
```

Deploy Nginx server

```bash
kubectl apply -f config/nginx-deployment.yaml
```

Create service for Nginx

```bash
kubectl apply -f nginx-service.yaml
```

## References

https://medium.com/@timhberry/deploy-a-highly-available-shared-storage-service-in-google-kubernetes-engine-with-regional-bbc87276c8eakubectl 
