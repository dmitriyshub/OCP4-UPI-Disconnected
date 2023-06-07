### Configure HTTPD
- Change Listen Port in `/etc/httpd/conf/httpd.conf`
___
#### Configure Firewall and Selinux and Start HTTPD:
```
$ sudo firewall-cmd --add-port=<port> --permanent 
$ sudo firewall-cmd --reload
$ semanage fcontext -a -t httpd_sys_content_t "/var/www/html/(/.*)?"
$ restorecon -R -v /var/www/html/pub
$ sudo systemctl enable –now httpd
```
___
[Return to main](../README.md)
