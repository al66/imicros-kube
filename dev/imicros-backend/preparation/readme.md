## Password generator
If not yet running, install the password generator as described [here](../../password-generator/readme.md).

## User/Role for cassandra access
Create a new cassandra user for the access by the core services.

1. Create the kubernetes secret with the logon data and a given name
2. Open a CQL shell
3. Create the cassandra user via CQL with the credentials of the kubernetes secret

Create the secret first, to give the password generator time to generate the password
```
$ kubectl apply -f dev/imicros-backend/secretCassandra.yaml
```

Check result with:
```
$ kubectl describe secret cassandra-user-credentials -n imicros
```
The secret should have now the annotation secret-generator.v1.mittwald.de/autogenerate-generated-at: ... and a password key.

Grap the generated password for the creation of the cassandra user with:
```
$ kubectl get secrets/cassandra-user-credentials -n imicros --template={{.data.password}} | base64 -d
```

We nned now a CQL shell to create the new user in cassandra.
Search for an cassandra operator pod with:
```
$ kubectl get pods -n cass-operator
NAME                                           READY   STATUS    RESTARTS        AGE
cass-operator-9db6fb86f-z72mr                  1/1     Running   0               20d
dev-cluster-dev-datacenter-default-sts-0       2/2     Running   0               10d
dev-cluster-dev-datacenter-default-sts-1       2/2     Running   0               10d
dev-cluster-dev-datacenter-default-sts-2       2/2     Running   0               10d
```

Login with the cluster super user:
```
$ CASS_USER=$(kubectl -n cass-operator get secret dev-cluster-superuser -o json | jq -r '.data.username' | base64 --decode)
$ CASS_PASS=$(kubectl -n cass-operator get secret dev-cluster-superuser -o json | jq -r '.data.password' | base64 --decode)
# in einen der oben gelisteten Pods einloggen
$ kubectl -n cass-operator exec -ti dev-cluster-dev-datacenter-default-sts-0 -c cassandra -- sh -c "cqlsh -u '$CASS_USER' -p '$CASS_PASS'"
Connected to dev-cluster at 127.0.0.1:9042.
[cqlsh 5.0.1 | Cassandra 3.11.7 | CQL spec 3.4.4 | Native protocol v4]
Use HELP for help.
dev-cluster-superuser@cqlsh>
```

Now create the role with the required grants:
```
> CREATE ROLE IF NOT EXISTS development WITH LOGIN = true AND PASSWORD = 'the previous grabed password';
> GRANT ALL PERMISSIONS on KEYSPACE "imicros_keys" to development;
> GRANT ALL PERMISSIONS on KEYSPACE "imicros_exchange" to development;
> GRANT ALL PERMISSIONS on KEYSPACE "imicros_auth" to development;
> GRANT ALL PERMISSIONS on KEYSPACE "imicros_flow" to development;
> GRANT ALL PERMISSIONS on KEYSPACE "imicros_decision" to development;
```

Finally check permissions:
```
> list all permissions of development;
role        | username    | resource                    | permission
-------------+-------------+-----------------------------+------------
 development | development |     <keyspace imicros_auth> |     CREATE
 development | development |     <keyspace imicros_auth> |      ALTER
 development | development |     <keyspace imicros_auth> |       DROP
 development | development |     <keyspace imicros_auth> |     SELECT
 development | development |     <keyspace imicros_auth> |     MODIFY
 development | development |     <keyspace imicros_auth> |  AUTHORIZE
 development | development | <keyspace imicros_decision> |     CREATE
 development | development | <keyspace imicros_decision> |      ALTER
 development | development | <keyspace imicros_decision> |       DROP
 development | development | <keyspace imicros_decision> |     SELECT
 development | development | <keyspace imicros_decision> |     MODIFY
 development | development | <keyspace imicros_decision> |  AUTHORIZE
 development | development | <keyspace imicros_exchange> |     CREATE
 development | development | <keyspace imicros_exchange> |      ALTER
 development | development | <keyspace imicros_exchange> |       DROP
 development | development | <keyspace imicros_exchange> |     SELECT
 development | development | <keyspace imicros_exchange> |     MODIFY
 development | development | <keyspace imicros_exchange> |  AUTHORIZE
 development | development |     <keyspace imicros_flow> |     CREATE
 development | development |     <keyspace imicros_flow> |      ALTER
 development | development |     <keyspace imicros_flow> |       DROP
 development | development |     <keyspace imicros_flow> |     SELECT
 development | development |     <keyspace imicros_flow> |     MODIFY
 development | development |     <keyspace imicros_flow> |  AUTHORIZE

(24 rows)
```

## Minio user
Cerate a minio user for the access via the development container.

1. Create the kubernetes secret with the logon data and a given name
2. Create the realated minio user with the minio console

Create the secret first, to give the password generator time to generate the password
```
$ kubectl apply -f dev/development/secretMinio.yaml
```

Check result with:
```
$ kubectl describe secret minio-user-credentials -n development
```
The secret should have now the annotation secret-generator.v1.mittwald.de/autogenerate-generated-at: ... and a password key.

Grap the generated password for the creation of the minio user with:
```
$ kubectl get secrets/minio-user-credentials -n development --template={{.data.password}} | base64 -d
```

Call the [minio console](http://minio-console.minio.svc.cluster.local:9090/login) (you must be connected with wireguard).
Under Administrator > Identity > Users cerate the new user 'development' with the previous grabed password and the policy 'readwrite'.


## Preparation finished
[Continue](../readme.md) now with the installation.