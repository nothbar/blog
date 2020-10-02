CentOS7安装和配置iptables防火墙


本文主要介绍一下CentOS7下如何安装和配置iptables防火墙。
环境配置

从CentOS7(RHEL7)开始，官方的标准防火墙设置软件从iptables变更为firewalld。 因此，为了使Fail2ban与iptables联动，需禁用自带的firewalld服务，同时安装iptables服务。

首先，使用如下命令停止和禁用自带的firewalld服务。
```
systemctl stop firewalld
systemctl mask firewalld
```

然后，使用如下命令安装iptables。

```
yum install -y iptables
yum update iptables 
yum install iptables-services
```

为了记录iptables防火墙丢弃的数据包到日志文件，还需修改/etc/rsyslog.conf文件。

```
vim  /etc/rsyslog.conf
```

填入以下内容。

```
kern.*     /var/log/iptables.log
```

重启rsyslog服务。

```
service rsyslog restart
```

另外，还需对日志文件每隔一段时间（一周）进行切割，所以需要编辑/etc/logrotate.d/syslog文件。

```
vim /etc/logrotate.d/syslog
```

然后填入以下内容。
```
/var/log/iptables.log
```

规则设定

关于iptables的书写规则网上已经有很多说明。本文列举一份针对Web服务器的iptables脚本然后对其进行说明。

以下是一份完整的iptables脚本。
```
iptables -P INPUT ACCEPT
iptables -F
iptables -X
iptables -Z
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A INPUT  -p tcp -j LOG --log-prefix "iptables denied"
iptables -P INPUT   DROP
iptables -P OUTPUT  ACCEPT
iptables -P FORWARD ACCEPT
```

然后重启防火墙。
```
service iptables save
service iptables restart
```
```
查看iptables现有规则

iptables -L -n

脚本说明

iptables -P INPUT ACCEPT

表示先允许所有的输入通过防火墙,以防远程连接断开。

iptables -F

表示清空所有默认规则。

iptables -X

表示清空所有自定义规则。

iptables -Z

表示将所有计数器归0。

iptables -A INPUT -i lo -j ACCEPT

表示允许来自于lo接口（本地访问）的数据包

iptables -A INPUT -p tcp --dport 22 -j ACCEPT

表示开放22端口(SSH)。

iptables -A INPUT -p tcp --dport 80 -j ACCEPT

表示开放80端口(HTTP)

iptables -A INPUT -p tcp --dport 443 -j ACCEPT

表示开放443端口(HTTPS)

iptables -A INPUT -m state --state  RELATED,ESTABLISHED -j ACCEPT

表示允许接受本机请求之后的返回数据。ESTABLISHED表示已建立的连接状态。RELATED表示该封包与本机发出的封包有关。

iptables -A INPUT  -p tcp -j LOG --log-prefix "iptables denied"

表示所有被丢弃的包都会被记录到/var/log/iptables.log文件中，且每条记录会以"iptables denied"作为前缀。

iptables -P INPUT DROP

表示其他入站一律丢弃

iptables -P OUTPUT ACCEPT

表示所有出站一律通过

iptables -P FORWARD DROP

表示所有转发一律通过
设定其他规则

如果要添加可信任网段：192.168.0.1/24，接受其所有请求。

iptables -A INPUT -s 192.168.0.1/24 -j ACCEPT

如果要添加可信任ip：192.168.0.1，接受其所有TCP请求。

iptables -A INPUT -p tcp -s 192.168.0.1 -j ACCEPT

如果要添加可信任ip：192.168.0.1，接受其对某个端口：3306的所有TCP请求。

iptables -A INPUT -p tcp -s 192.168.0.1 --dport 3306 -j ACCEPT

如果要封停一个IP：192.168.0.1。

iptables -A INPUT -s 192.168.0.1 -j DROP
```
