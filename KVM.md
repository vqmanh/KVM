# KVM
**Kiem tra he thong co support cho KVM hay ko**
*grep -e 'vmx' /proc/cpuinfo*
*lsmod | grep kvm*
<img src="https://imgur.com/wpKnkFb">
**Update va Cai dat**
*yum update -y*
*yum install qemu-kvm qemu-img virt-manager libvirt libvirt-python libvirt-client virtinstall virt-viewer bridge-utils -y*
**Start dich vụ**
*systemctl start libvirtd*
*systemctl enable libvirtd*
**Cài đặt networking**
**Tạo card br0**
*nmcli c add type bridge autoconnect yes con-name br0 ifname br0*
**Cấu hình card br0**
*vi /etc/sysconfig/network-scripts/ifcfg-br0*
```
TYPE=Bridge  
NAME=br0  
DEVICE=br0  
ONBOOT=yes  
BOOTPROTO=none  
HWADDR=00:0c:29:6c:77:e6  
IPADDR=66.0.0.199  
GATEWAY=66.0.0.1  
DNS=8.8.8.8  
NETMASK=255.255.255.0
```
**Chỉnh lại card cũ ens33**
*vi /etc/sysconfig/network-scripts/ifcfg-ens33*
```
BRIDGE=br0  
NAME=ens33  
DEVICE=ens33  
ONBOOT=yes  
BOOTPROTO=none  
HWADDR=00:0c:29:6c:77:e6
```
**Restart lại card mạng**
*/etc/init.d/network restart*
**Mở dịch vụ**
*virt-manager*

