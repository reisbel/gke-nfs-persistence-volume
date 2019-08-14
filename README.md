# gke-nfs-persistence-volume

Create a Network File System(NFS) Server with Google Compute Engine persistent disks and mount them to GKE containers.

## Steps

Clone repository

```bash
git clone https://github.com/reisbel/gke-nfs-persistence-volume.git && cd gke-nfs-persistence-volume
```

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

Create persistence disk

```bash
gcloud compute disks create --size=10GB --zone=$ZONE gce-nfs-disk
```

Create NFS Server

```bash
kubectl apply -f config/nfs-server.yaml
```

Create NFS service

```bash
kubectl apply -f config/nfs-server-service.yaml
```

Create Volume

```bash
kubectl apply -f config/pv-and-pvc-nfs.yaml
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
kubectl apply -f config/nginx-service.yaml
```

## References

https://medium.com/platformer-blog/nfs-persistent-volumes-with-kubernetes-a-case-study-ce1ed6e2c266


## License

MIT - See [LICENSE](LICENSE) for more information.
