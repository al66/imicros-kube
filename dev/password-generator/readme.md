We use a [password generator](https://github.com/mittwald/kubernetes-secret-generator) for the automtaic generation of secrets.

Fetch the repo...
```
$ microk8s helm repo add mittwald https://helm.mittwald.de
```

...Oor update, if already grabed...
```
$ microk8s helm repo update
```

...and install
```
$ microk8s helm upgrade --install kubernetes-secret-generator mittwald/kubernetes-secret-generator
```
