# NFS-Persistent-Volumes-with-Kubernetes-on-GKE

## Create Persistent Disk in Google Compute Engine
You need to change the zone to the zone of your Kubernetes cluster is in .
```
gcloud compute disks create --size=10GB --zone=asia-southeast1-a gce-nfs-disk
```

## Create NFS Server in GKE
You need to set the pdName in  gcePersistentDisk to the persistent disk you created in GCE.
Besides, you will mount the persistent disk you created to "/exports" of the volume in the NFS server container.
```
kubectl apply -f 001-nfs-server.yaml
```

## Create NFS Service
You will be exposing the NFS Server you created previously to cluster IP
```
kubectl apply -f 002-nfs-server-service.yaml
```

## Create NFS Persistent Volume and Persistent Volume Claims
Note that you have to put the the service discovery name in the form of <service-name>.<namespace>.svc.cluster.local in place of <ClusterIP> ib the following code snippet. In our case, because this is in the default namespace it will be nfs-service.default.svc.cluster.local. Besides, the path means the path exported by the server.
```
kubect apply -f 003-pv-pvc.yaml
```
## Create a Deployment of nginx for checking the NFS volume
```
kubectl apply -f 004-deployment.yaml
```

## Testing
get a shell into the running container of NFS Server 
**replace the pod name with your pod name**
```
kubectl exec -it nfs-server-5f58f8d764-s6ktw -- bash
```
Then cd into the /exports where you mount your persistent disk to create an index.html 
```
echo "Hello, NFS Storage NGINX" > index.html
```
After that get a shell into the running container of Nginx and cd into /usr/share/nginx/html. You should be able to see the index.html.


