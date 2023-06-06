#### Configure HAProxy on Internal Bastion and additional Server
- conf file path  `/etc/haproxy/haproxy.cfg`

```
$ firewall-cmd --add-port 22623/tcp --permanent 
$ firewall-cmd --add-port 6443/tcp --permanent 
$ firewall-cmd --add-service https --permanent 
$ firewall-cmd --add-service http --permanent 
$ firewall-cmd --add-port 9000/tcp --permanent 
$ firewall-cmd --reload
$ firewall-cmd --list-ports
```
```
$ setenforce 0
$ systemctl start haproxy
$ systemctl enable haproxy
$ cat /var/log/audit/audit.log | audit2allow -M haproxy
$ semodule -i haproxy.pp
$ setenforce 1
```