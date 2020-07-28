在CentOS6的上安装Windows2012R2的KVM虚拟机

1：上传cn_windows_server_2012_r2_vl_with_update_x64_dvd_6052729.iso
下载驱动盘
https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/virtio-win-0.1.141-1/virtio-win-0.1.141.iso


2：创建虚拟机
sudo virt-install --name=cn71012 --ram=10240 --vcpus=32 --boot=cdrom \
--os-type windows --os-variant win2k8 \
--disk path=/dev/vg_samsung500g/sys_cn71012_win2012r2,device=disk,cache=writeback,format=raw,bus=virtio \
--cdrom=/mnt/lv_data/iso/win2012r2/cn_windows_server_2012_r2_vl_with_update_x64_dvd_6052729.iso \
--network network:default,model=virtio \
--graphics vnc,password=123,port=59012,listen=0.0.0.0 \
--accelerate --hvm --noautoconsole

3：用tvnviewer.exe连接59012进行安装

4：放上virtio驱动盘
virsh attach-disk cn71012 /mnt/lv_data/iso/kvm/virtio-win-0.1.141.iso hdc --type cdrom
我选择D:\viostor\2k12R2\amd64\viostor.inf

5：换回Windows安装盘
virsh attach-disk cn71012 /mnt/lv_data/iso/win2012r2/cn_windows_server_2012_r2_vl_with_update_x64_dvd_6052729.iso hdc --type cdrom
安装完成


iptables -t nat -A PREROUTING -p tcp --dport 3389 -j DNAT --to 192.168.122.107:3389
iptables -A FORWARD -d 192.168.122.107/32 -p tcp -m state --state NEW -m tcp --dport 3389 -jACCEPT


virt-install \
--virt-type kvm \
--name cn71001 \
--ram 1024 --vcpus 2 \
--network network:default,model=virtio \
--disk path=/dev/vg_samsung500g/sys_cn71001_ikuaiv3,device=disk,cache=writeback,format=raw,bus=ide \
--cdrom=/mnt/lv_data/iso/iKuai8_x32_3.0.7_Build201804191004.iso \
--graphics vnc,password=88333775,port=59001,listen=0.0.0.0 \
--os-variant rhel6
