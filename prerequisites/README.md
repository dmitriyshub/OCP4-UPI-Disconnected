### Prerequisites
Choose the OCP version, get all the tools and packages to external Bastion and move it to the internal Bastion, get the pull secret and install all the tools on the external and internal Bastion, create SSH key and TLS certificate
#### Download the tools for the installation 

- `oc-mirror` **only on external** (Optional step because we can use oc tool instead):
```
$ mirror="https://mirror.openshift.com/pub/openshift-v4/clients/ocp/stable-4.10"
$ wget ${mirror}/oc-mirror.tar.gz
$ tar xvzf oc-mirror.tar.gz
$ chmod +x oc-mirror
$ sudo mv oc-mirror /usr/local/bin/
```
- `oc` and `kubectl` ****:
```
$ wget ${mirror}/openshift-client-linux-4.10.59.tar.gz
$ tar -xvf openshift-client-linux-4.10.59.tar.gz -C /usr/bin/
$ oc version && kubectl version
$ oc completion bash > /etc/bash_completion.d/openshift
$ source /etc/bash_completion.d/openshift (root and user)
```
- `openshift-install` **only on internal**
```
$ wget ${mirror}/openshift-install-linux-4.10.59.tar.gz
$ tar -xvf openshift-install-linux-4.10.59.tar.gz -C /usr/bin/
$ openshift-install version
$ openshift-install completion bash > /etc/bash_completion.d/openshift-install
$ source /etc/bash_completion.d/openshift-install (root and user)
```
#### Download and Install Packages
```
$ yum install yum-utils
$ yumdownloader --resolve <package1> && yumdownloader --resolve <package2>
$ scp <packages> <interna-bastionl-ip>:/path/to/dir/
$ rpm -ivh <packages>
```

#### Mandatory Packages

- `bind`
- `dhcp-server`
- `dhcp-common`
- `httpd-tools`
- `haproxy`
- `keepalived`


#### Move the tools to internal bastion

```
$ scp <tools&packages> <username>@<internal-ip-address>:/path/to/dir/
```

#### Create SSH Key (On internal bastion):
$ ssh-keygen -t rsa -b 4096 -N '' -f .ssh/ocp4
