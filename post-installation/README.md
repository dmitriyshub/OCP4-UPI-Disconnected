### Post installation
___
#### First Checks:
```
$ oc adm top nodes
$ oc get pods -n openshift-etcd
$ oc rsh etcd-master-01.dima.com -n openshift-etcd
$ etcdctl member list -w table
$ etcdctl endpoint health --cluster
$ etcdctl endpoint status -w table

$ host api.ocp4.dima.com
$ host api-int.ocp4.dima.com
$ host asd.apps.ocp4.dima.com
$ curl -k https://api.ocp4.dima.com:6443/version

$ oc whoami --show-console 
$ curl -kIs https://console-openshift-console.apps.ocp4.dima.com

$ oc -n openshift-image-registry get pods
$ oc debug nodes/master-01.dima.com
$ curl -kIs https://image-registry.openshift-image-registry.svc:5000/healthz

$ oc -n openshift-ingress get deployment
$ oc -n openshift-ingress  get pods -o wide

$ oc get sc
$ oc get network.config/cluster -o yaml
```
___
#### Local StorageClass and storage for image registry:

- #### [Storage Class file](storage/storageClass.yaml)

- #### [Registry Storage file](storage/registryStorage.yaml)
```
$ oc apply -f class.yml
$ oc apply -f registry-storage.yml
$ oc edit configs.imageregistry.operator.openshift.io -n openshift-image-registry
```
```
storage:
  pvc:
    claim: <claim-name>
```
```
$ oc patch configs.imageregistry.operator.openshift.io cluster --type merge --patch '{"spec":{"managementState":"Managed"}}'
$ oc patch configs.imageregistry.operator.openshift.io cluster --type merge --patch '{"spec":{"replicas":2}}'
$ oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
$ docker login -u openshift -p $(oc whoami -t) <registry_ip>:<port>
```
___
#### Configure HTPasswd provider:
```
$ htpasswd -c -B -b ~/DO280/labs/auth-provider/htpasswd admin redhat
$ htpasswd -b ~/DO280/labs/auth-provider/htpasswd username password
...and so on...

$oc create secret generic htpasswd-users  --from-file htpasswd=./htpasswd -n openshift-config
$ oc adm policy add-cluster-role-to-user cluster-admin admin
$ oc get oauth cluster     -o yaml > oauth.yaml && vi oauth.yaml
```
```
spec:
  identityProviders:
  - htpasswd:
      fileData:
        name: htpasswd-users
    mappingMethod: claim
    name: myusers
    type: HTPasswd
```
```
$ oc replace -f oauth.yaml
watch oc get pods -n openshift-authentication
```
- delete kubeadmin **IMPORTANT**
```
oc delete secrets kubeadmin -n kube-system
```
___
#### Configure ingress TLS 

- Create rootCA:
```
$ openssl genrsa -out rootCAKey.pem 2048
$ openssl req -x509 -sha256 -new -nodes -key rootCAKey.pem -days 3650 -out rootCACert.pem
$ openssl x509 -in rootCACert.pem -text
```
#### Update OpenShift:
```
cat > user-ca-bundle.yaml << EOF
apiVersion: v1
data:
  ca-bundle.crt: |
     -----BEGIN CERTIFICATE-----
     <---Root CA Certificate--->
     -----END CERTIFICATE-----
kind: ConfigMap
metadata:
  name: user-ca-bundle
namespace: openshift-config
EOF
```
```
$ oc create -f user-ca-bundle.yaml

$ oc edit proxy/cluster
trustedCA:     
  name: user-ca-bundle

$ oc edit cm trusted-ca-bundle -n openshift-config-managed
```
- (Copy/Paste the content of the Root CA certificate file at the beginning)

#### Configure Ingress TLS:
```
cat > csr_details.txt << EOF
[req]
default_bits = 4096
prompt = no
default_md = sha256
x509_extensions = req_ext
req_extensions = req_ext
distinguished_name = dn
[ dn ]
C=IL
ST=Center
L=TLV
O=Dima
OU=OpenShift
emailAddress=dima@dima.com
CN = *.apps.ocp.dima.com
[ req_ext ]
subjectAltName = @alt_names
[ alt_names ]
DNS.1 = *.apps.openshift.example.com
DNS.2 = apps.openshift.example.com
EOF
```
```
$ openssl genrsa -out router.key 4096
$ openssl req -new -sha256 -nodes -key router.key -out router.csr -config <(cat csr_details.txt)
$ openssl req -in router.csr -noout -text | grep -i dns
$ openssl x509 -req -extfile <(printf "subjectAltName=DNS:*.apps.ocp4.dima.com,DNS:apps.ocp4.dima.com") -days 120 -in router.csr -CA rootCACert.pem -CAkey rootCAKey.pem -CAcreateserial -out router.crt -sha256
$ openssl x509 -in router.crt -text
$ cat router.crt rootCACert.pem > router-bundle.crt
$ oc create secret tls router --cert=./router-bundle.crt --key=./router.key -n openshift-ingress
$ oc patch ingresscontrollers.operator default --type=merge -p '{"spec":{"defaultCertificate": {"name": "router"}}}' -n openshift-ingress-operator
$ cp *.crt /etc/pki/ca-trust/source/anchors/
$ update-ca-trust extract
$ oc get route -n openshift-console
```
___
[Return to main](../README.md)