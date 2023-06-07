### Configure Keepalived on Internal Bastion and HAProxy 
- [configuration file](./keepalived-master.conf) path  `/etc/keepalived/keepalived.conf` **on master server**
- [configuration file](./keepalived-backup.conf) path  `/etc/keepalived/keepalived.conf` **on backup server**
___
#### Start Keepalived and Configure Firewall:
```
$ firewall-cmd --add-rich-rule='rule protocol value="vrrp" accept' --permanent
$ firewall-cmd --reload
$ systemctl enable –now keepalived
```
___
[Return to main](../README.md)
