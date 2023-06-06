#### Configure DNS Server
- conf file path  `/etc/named.conf`
- zone file path `/var/named/.`
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
... and so on ...
```