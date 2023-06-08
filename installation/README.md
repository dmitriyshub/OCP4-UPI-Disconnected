### OCP4 Installation
___
#### Create the install config file or copy from git:
```
openshift-install create install-config
```
___
#### Create new directory (Important) and copy the `install-config.yaml` inside:
- the install-config file will be consumed by openshift-install so create a backup before you run the command
___
#### Create the Manifest from `install-config.yaml`:
- Change the manifests to your needs (Optional)
```
openshift-install create manifests --dir=/path/to/dir
```
___
#### Create the ignition files from the manifests:
```
openshift-install create ignition-configs --dir=/path/to/dir
```
___
#### **OVA mode**:
#### Encode the ignition files:
```
$ base64 -w0 bootstrap.ign > bootstrap.b64 
$ base64 -w0 master.ign > master.b64 
$ base64 -w0 worker.ign > worker.b64 
```
#### Add additional properties for all cluster nodes in Vsphere:

`Key: guestinfo.ignition.config.data.encoding`

`Value: base64`

`Key: guestinfo.ignition.config.data`

`Value: encoded ignition file`

#### Or Configure httpd server with ignition files:
- create and encode [`append-file.ign`](./append-file.ign) file, and pass it to guestinfo.ignition.config.data in Vsphere vm properties
___
#### **ISO mode**:
#### Configure HTTPD:
- Copy the ignition files to httpd dir (Typically in `/var/www/html`), configure port in conf file, and add port to firewalld
Turn on vm`s and start coreos-installer on every vm
* without dhcp configure static ip`s with nmtui/nmcli
```
coreos-installer install --copy-network --insecure-ignition --ignition-url=http://192.168.1.2:9091/bootstrap.ign /dev/sda
```
- And reboot vm`s
- Do it for every vm 
- Change the file name for masters and workers
___
#### Installation begins
- The installation begins after you power on the bootstrap, the bootstrap waits for the master machines, and you can delete the bootstrap after the control planes are ready
- After the masters you can run the workers and approve csr`s
#### Catch the screen and debug during the installation:
```
$ openshift-install wait-for bootstrap-complete --log-level=debug
$ openshift-install wait-for install-complete --log-level=debug
```
___
#### API Authentication:
```
$ export KUBECONFIG=~/ocp4-new/auth/kubeconfig
```
___
#### CSR approval:
```
$ oc get csr | grep -i pending
$ oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | xargs oc adm certificate approve
```
- or 
```
$ for i in `oc get csr --no-headers | grep -i pending |  awk '{ print $1 }'`; do oc adm certificate approve $i; done
```
___
#### **Troubleshooting**:
#### On Cluster with API:
```
$ oc get nodes
$ oc get clusterversion -o json | jq -r '.items[].status.conditions[]'
$ oc get co 
$ watch "oc get clusterversion;echo;oc get co;echo;oc get nodes"
```

#### On Nodes with SSH:
```
$ ssh -i <path-to-key> core@server-ip
$ sudo crictl ps
$ journalctl -b -f -u release-image.service -u bootkube.service
$ netstat -atunp | grep LISTEN
```
---
[Return to main](../README.md)