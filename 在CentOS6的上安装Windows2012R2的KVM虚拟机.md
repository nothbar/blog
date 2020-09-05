如果你的系统是最小化安装的，那你应该安装以下所需软件：
```

# yum groupinstall "GNOME 桌面"     //安装GNOME桌面环境

查看cpu是否支持

# grep -E 'svm|vmx' /proc/cpuinfo

- vmx is for Intel processors

- svm is for AMD processors



安装虚拟化软件

# yum install epel-rpm-macros.noarch       //安装epel源

# yum install qemu qemu-img qemu-kvm  libvirt libvirt-python libguestfs-tools virt-install

# yum install virt-manager virt-viewer     //安装图形化工具

# systemctl enable libvirtd                

# systemctl start libvirtd



检查KVM模块是否安装

[root@localhost ~]# lsmod |grep kvm

kvm_intel             174250  0 

kvm                   570658  1 kvm_intel

irqbypass              13503  1 kvm
```

在CentOS6的上安装Windows2012R2的KVM虚拟机

1：上传cn_windows_server_2012_r2_vl_with_update_x64_dvd_6052729.iso
下载驱动盘
https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/virtio-win-0.1.141-1/virtio-win-0.1.141.iso


2：创建虚拟机
```
sudo virt-install --name=cn71012 --ram=10240 --vcpus=32 --boot=cdrom \
--os-type windows --os-variant win2k8 \
--disk path=/dev/vg_samsung500g/sys_cn71012_win2012r2,device=disk,cache=writeback,format=raw,bus=virtio \
--cdrom=/mnt/lv_data/iso/win2012r2/cn_windows_server_2012_r2_vl_with_update_x64_dvd_6052729.iso \
--network network:default,model=virtio \
--graphics vnc,password=123,port=59012,listen=0.0.0.0 \
--accelerate --hvm --noautoconsole
```
3：用tvnviewer.exe连接59012进行安装

4：放上virtio驱动盘
```
virsh attach-disk cn71012 /mnt/lv_data/iso/kvm/virtio-win-0.1.141.iso hdc --type cdrom
我选择D:\viostor\2k12R2\amd64\viostor.inf
```

5：换回Windows安装盘
``
virsh attach-disk cn71012 /mnt/lv_data/iso/win2012r2/cn_windows_server_2012_r2_vl_with_update_x64_dvd_6052729.iso hdc --type cdrom
``
安装完成

```
iptables -t nat -A PREROUTING -p tcp --dport 3389 -j DNAT --to 192.168.122.107:3389
iptables -A FORWARD -d 192.168.122.107/32 -p tcp -m state --state NEW -m tcp --dport 3389 -jACCEPT
```
```
virt-install \
--virt-type kvm \
--name cn71001 \
--ram 1024 --vcpus 2 \
--network network:default,model=virtio \
--disk path=/dev/vg_samsung500g/sys_cn71001_ikuaiv3,device=disk,cache=writeback,format=raw,bus=ide \
--cdrom=/mnt/lv_data/iso/iKuai8_x32_3.0.7_Build201804191004.iso \
--graphics vnc,password=88333775,port=59001,listen=0.0.0.0 \
--os-variant rhel6
```


```
virt-install \
--name enwin2k8r2 \
--ram 2048 \
--vcpus 2 \
--os-type windows \
--os-variant win2k8 \
--network bridge=br0 \
--accelerate \
--disk path=/data/images/win2008r2en.img,format=qcow2,size=15 \
--cdrom=/mnt/en_windows_server_2008_r2_standard_enterprise_datacenter_and_web_with_sp1_vl_build_x64_dvd_617403.iso \
--graphics vnc,listen=0.0.0.0,password=hm123,port=5910 \
--noautoconsole
```

```
--name  虚拟机名称
--ram     内存大小
--vcpus   处理器核数
--os-type 系统类型
--os-variant  系统版本
--network   网络类型
--accelerate  优化选项
--disk    磁盘镜像
--cdrom  安装镜像
--noautoconsole  不启动kvm安装控制台
```

最后
https://ngx.hk/2016/10/07/kvm%E5%AE%89%E8%A3%85win-server-2012-r2.html
安装成功
```
 wget https://fedorapeople.org/groups/virt/virtio-win/virtio-win.repo -O /etc/yum.repos.d/virtio-win.repo
yum install virtio-win
```
```
chmod  0660 cn_windows_server_2012_r2_x64_dvd_2707961.iso
  297  virt-install --name 2012r2 --memory 2048 --vcpus sockets=1,cores=2,threads=2 --disk device=cdrom,path=/home/cn_windows_server_2012_r2_x64_dvd_2707961.iso --disk device=cdrom,path=/usr/share/virtio-win/virtio-win.iso --disk path=/home/data/images/win2012r2.img,size=100,bus=virtio --network bridge=virbr0,model=virtio --noautoconsole --accelerate --graphics vnc,listen=0.0.0.0,password=Zxc1986126,port=25910
```
