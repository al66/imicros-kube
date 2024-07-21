### Preparation

Image bitnami/cassandra requires change owner of mounted volume.

```bash
$ sudo chown 1001 /mnt/cassandra-dev-1
```


### Deployment
Go back to admin home.\

```bash
$ rm -rf imicros-kube
$ git clone --depth 1 https://github.com/al66/imicros-kube.git
$ kubectl apply -f ./imicros-kube/dev/cassandra
```

