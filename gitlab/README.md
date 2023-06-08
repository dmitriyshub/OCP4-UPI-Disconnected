### Gitlab server installation
#### Prerequisites:
- Create new VM
- Configure DNS resolution
- Configure dhcp for the server

#### Installation:
```
sudo yum install -y curl policycoreutils-python openssh-server perl
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo systemctl reload firewalld
sudo yum install postfix
sudo systemctl enable --now postfix
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash
sudo EXTERNAL_URL="https://gitlab.dima.com" yum install -y gitlab-ee
sudo vi /etc/gitlab/gitlab.rb
```