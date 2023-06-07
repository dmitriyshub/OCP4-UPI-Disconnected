### Configure HAProxy on Internal Bastion and HAProxy Server
(You can separate HAProxy from Bastion and create 2 HAProxy servers)
- [configuration file](./haproxy.cfg) path  `/etc/haproxy/haproxy.cfg`
___
#### Configure Firewall:
```
$ firewall-cmd --add-port 22623/tcp --permanent 
$ firewall-cmd --add-port 6443/tcp --permanent 
$ firewall-cmd --add-service https --permanent 
$ firewall-cmd --add-service http --permanent 
$ firewall-cmd --add-port 9000/tcp --permanent 
$ firewall-cmd --reload
$ firewall-cmd --list-ports
```
___
#### Start HAProxy and Configure Selinux:
```
$ setenforce 0
$ systemctl enable --now haproxy
$ cat /var/log/audit/audit.log | audit2allow -M haproxy
$ semodule -i haproxy.pp
$ setenforce 1
```
___
[Return to main](../README.md)
