### Deploy minio
Go back to admin home.\

```bash
$ rm -rf imicros-kube
$ git clone --depth 1 https://github.com/al66/imicros-kube.git
$ kubectl apply -f ./imicros-kube/dev/minio
```

### New secret for root user
Update first the secret minio-root which can be used for the first login. This secret will be referenced in the deployment.\

For the user you can use an online available version 4 uuid generator or just name it root.\
For the password you can use the apg tool, e.g. with `apg -a 1`.\

If not yet base64 encoded, the user and password must be converted to base64:
```bash
$ echo your-root-user-password | base64
eW91ci1yb290LXVzZXItcGFzc3dvcmQK
$ echo your-root-user-id | base64
eW91ci1yb290LXVzZXItaWQK
```

To create the secret we use nano.

Bash admin
```bash
$ mkdir minio
$ cd minio
$ nano secret.yaml
```

Use this template and maintain password and user:
```yaml
kind: Secret
apiVersion: v1
metadata:
  name: minio-root
  namespace: minio
data:
  password: eW91ci1yb290LXVzZXItcGFzc3dvcmQK
  user: eW91ci1yb290LXVzZXItaWQK
type: Opaque
```

Bash admin
```bash
$ kubectl apply -f .
```

Restart the minio deplyment.
```bash
$ kubectl rollout restart -n minio deployment minio-deployment
```



