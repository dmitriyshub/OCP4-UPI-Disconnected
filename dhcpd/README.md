### Configure DHCP Server
- [configuration file](./dhcpd.conf) path  `/etc/dhcp/dhcpd.conf`
```
$ sudo systemctl enable –now dhcpd
$ sudo firewall-cmd --add-service=dhcp --permanent 
$ sudo firewall-cmd --reload
```

[Return to main](../README.md)
