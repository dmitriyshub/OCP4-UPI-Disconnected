### Mirror Operators
___
#### Download oc-mirror:
```
$ wget https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/oc-mirror.tar.gz
$ tar xvf oc-miiror.tar.gz
$ chmod+x oc-mirror
```
#### Authentication
- Copy your pull secret to `{XDG_RUNTIME_DIR}/containers/auth.json` or `$HOME/.config/containers/auth`
___
#### List Operator Catalog and choose one:
```
$ oc-mirror list operators --catalog=registry.redhat.io/redhat/redhat-operator-index:v4.10 
```
#### Create [imageset-config.yaml](./imageset-config.yaml)
____
#### Mirror operator locally and move it to internal bastion:
```
$ oc-mirror --config imageset-config.yaml file:////path/to/dir --continue-on-error
$ scp /path/to/dir/* username@ip-address:/path/to/file
```
___
#### Mirror operator from locally to local registry:
```
$ oc-mirror --from /path/to/dir docker://registry.dima.com:5000
```
___
#### Change the name in result yamls and apply them:
```
$ oc apply -f ./oc-mirror-workspace/results-1686136072/imageContentSourcePolicy.yaml
$ oc apply -f ./oc-mirror-workspace/results-1686136072/catalogSource-redhat-operator-index.yaml
```
___
[Return to main](../README.md)
