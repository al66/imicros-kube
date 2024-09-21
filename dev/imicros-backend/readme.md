This creates a deveolpment container based on node with the environment variables for access to the services in the cluster: Minio, Cassandra, Kafka, Redis.

Check the [preperation steps](preparation/readme.md) first.


```
$ kubectl apply -f ./dev/imicros-backend
```