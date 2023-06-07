### Configure DNS Server
- [conf file path](./dns.zone.rev)  `/etc/named.conf`
- [zone file](./dns.zone) path `/var/named/dns.zone`
- [reverse zone](./named.conf) file path `/var/named/dns.zone.rev`
```
$ chown named:named /var/named -R
$ sudo systemctl enable –now named
$ firewall-cmd --permanent --add-service=dns 
$ firewall-cmd --reload
```

#### Check the DNS
```
$ dig api.ocp4.dima.com
$ dig api-int.ocp4.dima.com
$ dig whatever.apps.ocp4.dima.com
# ... and so on ...
```

[Return to main](../README.md)
