```
yum -y groups install "GNOME Desktop"
yum install xrdp
rpm -Uvh http://mirrors.ustc.edu.cn/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
wget http://mirrors.ustc.edu.cn/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
yum install  epel* -y
yum --enablerepo=epel -y install xrdp
systemctl start xrdp
systemctl enable xrdp
firewall-cmd --permanent --zone=public --add-port=3389/tcp
firewall-cmd --reload
```
