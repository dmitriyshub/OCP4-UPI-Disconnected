### Configure DHCP Server
- [configuration file](./dhcpd.conf) path  `/etc/dhcp/dhcpd.conf`
___
#### Start DHCP and Configure Firewall:
```
$ sudo systemctl enable –now dhcpd
$ sudo firewall-cmd --add-service=dhcp --permanent 
$ sudo firewall-cmd --reload
```
___
[Return to main](../README.md)
