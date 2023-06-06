### Configure Keepalived on Internal Bastion and HAProxy 
- conf file path  `/etc/keepalived/keepalived.conf`
```
$ firewall-cmd --add-rich-rule='rule protocol value="vrrp" accept' --permanent
$ firewall-cmd --reload
$ systemctl enable –now keepalived
```

[Return to main](../README.md)