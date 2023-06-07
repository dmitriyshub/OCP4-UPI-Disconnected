# Image Registry

#### Download RedHat [PullSecret](https://console.redhat.com/openshift/downloads)

- Check the PullSecret:
```
$ python3 -m json.tool pull-secret.txt > pull-secret.json
$ cat pull-secret.json | jq . -c > pull-secret-oneline.json
$ sudo podman pull --authfile ~/pull-secret-oneline.json registry.redhat.io/ubi8/ubi:latest 
```
#### Create the Registry Directories:
- auth dir (registry username and password)
- certs dir (for the registry tls certificate)
- data dir (for the registry mirror data)
- images dir (for the registry docker image)
- tools dir (for the scripts install config file and ocp install tools)
- secrets dir (for the pull secret and mirror registry)

```
$ export REGISTRY_BASE="/opt/registry"
$ mkdir -p ${REGISTRY_BASE}/{auth,certs,data,downloads}
$ sudo mkdir -p ${REGISTRY_BASE}/{auth,certs,data,downloads}
$ sudo mkdir -p ${REGISTRY_BASE}/downloads/{images,tools,secrets}
$ tree /opt/registry/
```

- Add to host file `127.0.0.1 registry.dima.com`
- Create Registry TLS Certificate with [Answer file](opt/registry/certs/csr_answer.txt):

```
$ openssl req -newkey rsa:4096 -nodes -sha256 -keyout domain.key -x509 -days 3650 -extensions 'req_ext' -out domain.crt -config csr_answer.txt
$ sudo cp ${REGISTRY_BASE}/certs/domain.crt /etc/pki/ca-trust/source/anchors/
$ sudo update-ca-trust extract
```
```
$ sudo htpasswd -bBc ${REGISTRY_BASE}/auth/htpasswd <username> <password>
```
```
$ sudo firewall-cmd --add-port=5000/tcp --zone=public --permanent
$ sudo firewall-cmd --reload

```
#### Create the Docker Registry [shell script](opt/registry/downloads/tools/start_registry.sh):
```
sudo chmod a+x ${REGISTRY_BASE}/downloads/tools/start_registry.sh
./{REGISTRY_BASE}/downloads/tools/start_registry.sh
curl -u <username>:<password> -k https://registry.dima.com:5000/v2/_catalog
```

#### Prepare the Env Variables:
```
$ export REGISTRY_BASE="/opt/registry"
$ export LOCAL_REGISTRY='registry.dima.com:5000'
$ export REGISTRY_BASE="/opt/registry"
$ export OCP_RELEASE=4.10.59
$ echo "export OCP_RELEASE=${OCP_RELEASE}" >> ${REGISTRY_BASE}/downloads/tools/env_ocp
```
```
$ export OCP_VERSION=4.10
$ echo "export OCP_VERSION=4.10" >> ${REGISTRY_BASE}/downloads/tools/env_ocp
$ export OCP_RELEASE="${OCP_RELEASE}-x86_64"
$ export LOCAL_REPOSITORY='ocp/openshift4'
$ export PRODUCT_REPO='openshift-release-dev'
$ export LOCAL_SECRET_JSON="${REGISTRY_BASE}/downloads/secrets/pull-secret-bundle.json"
$ export RELEASE_NAME="ocp-release" 
$ echo "export LOCAL_REGISTRY='registry:5000'">>${REGISTRY_BASE}/downloads/tools/env_ocp
$ echo '[[ ! ${OCP_RELEASE} =~ 'x86_64' ]] && export OCP_RELEASE="${OCP_RELEASE}-x86_64"' >> ${REGISTRY_BASE}/downloads/tools/env_ocp
$ echo "export LOCAL_REPOSITORY='ocp/openshift4'" >> ${REGISTRY_BASE}/downloads/tools/env_ocp

$ echo "export PRODUCT_REPO='openshift-release-dev'" >> ${REGISTRY_BASE}/downloads/tools/env_ocp
$ echo 'export LOCAL_SECRET_JSON="${REGISTRY_BASE}/downloads/secrets/pull-secret-bundle.json"' >> ${REGISTRY_BASE}/downloads/tools/env_ocp
$ echo 'export RELEASE_NAME="ocp-release"' >> ${REGISTRY_BASE}/downloads/tools/env_ocp
```

#### Mirror Registry:
```
$ oc adm -a ${LOCAL_SECRET_JSON} release mirror --from=quay.io/${PRODUCT_REPO}/${RELEASE_NAME}:${OCP_RELEASE} --to=${LOCAL_REGISTRY}/${LOCAL_REPOSITORY} --to-release-image=${LOCAL_REGISTRY}/${LOCAL_REPOSITORY}:${OCP_RELEASE} 2>&1 | tee ${REGISTRY_BASE}/downloads/secrets/mirror-output.txt

$ oc adm -a ${LOCAL_SECRET_JSON} release extract --command=openshift-install "${LOCAL_REGISTRY}/${LOCAL_REPOSITORY}:${OCP_RELEASE}"
```

#### Move Registry to Internal Server:
```
$ podman stop my-registry
$ podman save docker.io/library/registry:2 -o ${REGISTRY_BASE}/downloads/images/registry.tar
$ tar -zcf ocp410-registry.tar.gz *
$ md5sum ocp410-registry.tar.gz > md5.check
$ split -b 1G ../ocp410-registry.tar.gz "ocp410-registry.tar.gz.part"
$ scp * dshtranv@192.168.1.2:/home/dshtranv/ocp/registry/
$ cat ocp410-registry.tar.gz.part* > ocp410-registry.tar.gz
$ md5sum ocp410-registry.tar.gz 
```
- Extract the data and run the Registry with the same shell script


#### Registry Troubleshooting:
```
$ podman pull --authfile /opt/registry/downloads/secrets/pull-secret-bundle.json  registry.dima.com:5000/ocp/openshift4@sha256:3b228c6825f3d0eb34285084c538801d52fa24dce1db87293e8cc9accd83aaa1
$q
$ curl -u root:redhat -k https://registry.dima.com:5000/v2/ocp/openshift4/tags/list | jq
$ skopeo inspect --authfile pull-secret-bundle.json  docker://registry.dima.com:5000/ocp/openshift4
$ skopeo inspect --authfile pull-secret-bundle.json  docker://registry.dima.com:5000/ocp/openshift4:4.10.59-x86_64-cluster-csi-snapshot-controller-operator
```

[Return to main](../README.md)