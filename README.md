### OpenShift UPI Disconnected Installation mode on Vsphere
- I choose the 4.10.59 version and coreOS 4.10.37, but you can choose another version. **All tools need to be in the same version, coreOS can be smaller** 
- [4.10 Mirror versions](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/stable-4.10/)
___
#### Environment:
- Separate VLAN (Optional)
- External Bastion (2 NIC)
- Internal Bastion
- 2 HAProxy Server (You can separate HAProxy from Bastion and create 2 HAProxy server)
- CoreOS [OVA](https://mirror.openshift.com/pub/openshift-v4/x86_64/dependencies/rhcos/4.10/latest/rhcos-4.10.37-x86_64-vmware.x86_64.ova) or [ISO](https://mirror.openshift.com/pub/openshift-v4/x86_64/dependencies/rhcos/4.10/4.10.37/rhcos-4.10.37-x86_64-live.x86_64.iso) Template
- Bootstrap Server
- 3 Master Servers
- 3 Worker Servers

![OCP Architecture](/images/OCP%20disconnected%20installation.jpg)
___
### Steps:

#### [Prerequisites](/prerequisites/README.md) 

#### [DNS configuration](/named/README.md) 

#### [DHCP configuration](/dhcpd/README.md) 

#### [HAProxy configuration](/haproxy/README.md) 

#### [KeepAlived configuration](/keepalived/README.md) 

#### [Image-Registry](/image-registry/README.md)

#### [OCP4 Installation](/installation/README.md)

#### [OCP4 Post Installation](/post-installation/README.md)

#### [Mirror Operators](/mirror/README.md)

___
<span style="color:red">
<b>IMPORTANT</b></span>

#### **For security reasons, you never put your secrets and private keys in git**, and I do it because it's not actual production files, and it's for learning/tutorial purposes only!