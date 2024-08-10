

Install the minio operator first!

https://min.io/docs/minio/kubernetes/upstream/operations/installation.html#deploy-operator-kubernetes


Prerequisite...
```
$ sudo snap install yq
yq v4.44.2 from Mike Farah (mikefarah) installed```
```

Add to repo...
```
$ sudo microk8s helm repo add minio-operator https://operator.min.io
"minio-operator" has been added to your repositories
```

Check repo...
```
$ sudo microk8s helm search repo minio-operator
NAME                            CHART VERSION   APP VERSION     DESCRIPTION                    
minio-operator/minio-operator   4.3.7           v4.3.7          A Helm chart for MinIO Operator
minio-operator/operator         6.0.1           v6.0.1          A Helm chart for MinIO Operator
minio-operator/tenant           6.0.1           v6.0.1          A Helm chart for MinIO Operator
```

Install operator...
```
$ sudo microk8s helm install --namespace minio-operator --create-namespace operator minio-operator/operator
NAME: operator
LAST DEPLOYED: Sat Aug 10 10:35:13 2024
NAMESPACE: minio-operator
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Verify result...
```
$ kubectl get all -n minio-operator
NAME                                 READY   STATUS    RESTARTS   AGE
pod/minio-operator-744bc4b6f-6vnp5   1/1     Running   0          43s
pod/minio-operator-744bc4b6f-sjlkw   1/1     Running   0          43s

NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/operator   ClusterIP   10.152.183.44   <none>        4221/TCP   44s
service/sts        ClusterIP   10.152.183.78   <none>        4223/TCP   44s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/minio-operator   2/2     2            2           44s

NAME                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/minio-operator-744bc4b6f   2         2         2       43s
```
