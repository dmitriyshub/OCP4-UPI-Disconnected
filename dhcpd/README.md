### Configure DHCP Server
- conf file path  `/etc/dhcp/dhcpd.conf`
```
$ sudo systemctl enable –now dhcpd
$ sudo firewall-cmd --add-service=dhcp --permanent 
$ sudo firewall-cmd --reload
```