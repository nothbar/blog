```bash
yum -y groups install "GNOME Desktop"
yum install  epel* -y
yum --enablerepo=epel -y install xrdp
systemctl start xrdp
systemctl enable xrdp
firewall-cmd --permanent --zone=public --add-port=3389/tcp
firewall-cmd --reload
```


安装脚本
```bash
#!/bin/sh
yum grouplist
yum -y install epel-release && yum -y groupinstall Xfce
yum -y install xrdp
cd 
echo "xfce4-session" > ~/.Xclients
chmod +x .Xclients
sed -i "s/ssl_protocols=/; ssl_protocols=/g" /etc/xrdp/xrdp.ini
sed -i "58i ssl_protocols=TLSv1, TLSv1.3, TLSv1.1, TLSv1.2, TLSv3" /etc/xrdp/xrdp.ini
systemctl start xrdp && systemctl enable xrdp
firewall-cmd --add-port=3389/tcp --zone=public --permanent
firewall-cmd --reload
curl ifconfig.me
reboot
```
