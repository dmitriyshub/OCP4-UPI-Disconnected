### OCP4 Installation
#### Create the install config file or copy from git
```
openshift-install create install-config
```
#### Create new directory (Important) and copy the install-config.yaml inside
#### Create the Manifest from install-config.yaml
```
openshift-install create manifests --dir=/path/to/dir
```
#### Create the ignition files from the manifests
```
openshift-install create ignition-configs --dir=/path/to/dir
```
#### **OVA mode**:
#### Encode the ignition files:
```
$ base64 -w0 bootstrap.ign > bootstrap.b64 
$ base64 -w0 master.ign > master.b64 
$ base64 -w0 worker.ign > worker.b64 
```
#### Add additional properties for all cluster nodes:

`Key: guestinfo.ignition.config.data.encoding`

`Value: base64`

`Key: guestinfo.ignition.config.data`

`Value: encoded ignition file`

#### Or Configure httpd server with ignition files
- create and encode [`append-file.ign`](./append-file.ign) file, and pass it to guestinfo.ignition.config.data

#### **ISO mode**:
#### Configure HTTPD:
- Copy the ignition files to httpd dir (Typically in `/var/www/html`), configure port in conf file, and add port to firewalld
Turn on vm`s and start coreos-installer on every vm
* without dhcp configure static ip`s with nmtui/nmcli
```
coreos-installer install --copy-network --insecure-ignition --ignition-url=http://192.168.1.2:9091/bootstrap.ign /dev/sda
```
- And reboot vm`s

#### Catch the screen and debug during the installation:
```
$ openshift-install wait-for install-complete --log-level=debug
$ openshift-install wait-for bootstrap-complete --log-level=debug
```
#### API Authentication:
```
$ export KUBECONFIG=~/ocp4-new/auth/kubeconfig
```
#### CSR approval:
```
$ oc get csr | grep -i pending
$ oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | xargs oc adm certificate approve
```
- or 
```
$ for i in `oc get csr --no-headers | grep -i pending |  awk '{ print $1 }'`; do oc adm certificate approve $i; done
```

#### **Troubleshooting**:
#### With API
```
$ oc get nodes
$ oc get clusterversion -o json | jq -r '.items[].status.conditions[]'
$ oc get co 
$ watch "oc get clusterversion;echo;oc get co;echo;oc get nodes"
```
#### On Nodes:
```
$ ssh -i <path-to-key> core@server-ip
$ sudo crictl ps
$ journalctl -b -f -u release-image.service -u bootkube.service
$ netstat -atunp | grep LISTEN
```

[Return to main](../README.md)