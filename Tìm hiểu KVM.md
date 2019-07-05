# Tìm hiểu và cài đặt KVM trên CentOS7

## Mục lục
[1. KVM là gì?](#dinhnghia)

[2. Cách cài đặt KVM](#caidat)

  - [Chuẩn bị](#cb)
  - [Các bước cài đặt](#cai)

[3. Tạo và quản lí máy ảo với virt-manager](#tao)

---
### <a name = "dinhnghia"> 1. KVM là gì?

KVM (viết tắt của Kernel Virtualization Machine) là công nghệ ảo hóa phần cứng. Có nghĩa là OS (hệ điều hành) chính mô phỏng phần cứng cho các OS khác để chạy trên đó.

KVM VPS không có tài nguyên dùng chung, tất cả đã được mặc định sẵn và chia sẻ. Điều đó có nghĩa là RAM của mỗi VPS KVM đã được phân bổ sẵn cho từng gói VPS để sở hữu, tận dụng hoàn toàn 100% và không bị chia sẻ ra ngoài. Đồng nghĩa với việc VPS KVM sẽ hoạt động ổn định hơn mà không bị ảnh hưởng bởi các VPS khác cùng hoạt động trên hệ thống. Tương tự điều đó với disk space, tài nguyên ổ cứng cũng đã được xác định sẵn và không bị vay mượn bởi các VPS khác.
### <a name = "caidat"> 2. Cách cài đặt KVM
#### <a name = "cb"> Chuẩn bị:
Một máy chạy hệ điều hành CentOS 7 với cấu hình 2 CPU, 2 GB RAM và 10 GB disk.

#### <a name = "cai"> Các bước cài đặt:

***Bước 1: Update, cài đặt KVM và các gói phụ trợ liên quan***

`yum update -y`

`yum install qemu-kvm libvirt bridge-utils virt-manager -y`

Trong đó:

  - *qemu-kvm*: Phần phụ trợ cho KVM.
  - *libvirt-bin*: cung cấp libvirt mà bạn cần quản lý qemu và KVM bằng libvirt.
  - *bridge-utils*: chứa một tiện ích cần thiết để tạo và quản lý các thiết bị bridge.
  - *virt-manager*: cung cấp giao diện đồ họa để quản lý máy ảo.


***Lưu ý: Nếu bạn sử dụng VMWare, hãy nhớ bật chức năng ảo hóa Intel VT-x/EPT hoặc AMD-V/RVI***


 <img src="https://i.imgur.com/te8RkiW.png">

***Bước 2: Kiểm tra hệ thống có hỗ trợ KVM hay khônng***


  `grep -e 'vmx' /proc/cpuinfo`

  *Nếu kết quả trả về là khác 0 thì CPU có hỗ trợ*

  ***Bước 3: Kiểm tra để chắc chắn rằng KVM đã được cài đặt***

  `lsmod | grep kvm`

  <img src="https://i.imgur.com/wpKnkFb.jpg">

  
***Bước 4: Start dịch vụ***

  `systemctl start libvirtd`
  
  `systemctl enable libvirtd`
  

***Bước 5: Tạo, cấu hình card bridge br0***

  `nmcli c add type bridge autoconnect yes con-name br0 ifname br0`
  
***Cấu hình card br0***

  `vi /etc/sysconfig/network-scripts/ifcfg-br0`
  
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
***Chỉnh lại card cũ ens33***

  `vi /etc/sysconfig/network-scripts/ifcfg-ens33`
```
BRIDGE=br0  
NAME=ens33  
DEVICE=ens33  
ONBOOT=yes  
BOOTPROTO=none  
HWADDR=00:0c:29:6c:77:e6
```
*Lưu ý: Không nên xóa card ens33 khi bạn sử dụng môi trường lab VMware. Vì khi bạn đang SSH vào sẽ bị mất kết nối*

***Bước 6: Restart lại card mạng***

  `/etc/init.d/network restart`

***Reboot***

### <a name = "tao"> 3. Tạo và quản lí máy ảo với virt-manager

***Chú ý: Nên sử dụng MobaXterm để SSH vào thì mới có thể sử dụng được virt-manager***


***Bước 1: Tiến hành download file iso và đặt tại thư mục*** ```/var/lib/libvirt/images```

`wget http://mirrors.nhanhoa.com/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1611.iso /var/lib/libvirt/images`


*Hoặc bạn có thể đưa trực tiếp file ISO vào thư mục /var/lib/libvirt/images với WinSCP hoặc dùng MobaXterm*

<img src=https://i.imgur.com/nFb8ZXI.jpg>

***Bước 2: Mở virt-manager***

`virt-manager`

Sau khi hiển thị giao diện, bạn chọn  File => New Virtual Machine

Sử dụng tập tin ISO làm phương tiện cài đặt.

<img src=https://i.imgur.com/dwygI4D.jpg>


Sau đó Brown đến thư mục chứa file ISO

<img src=https://i.imgur.com/bcroWxc.jpg>

Chọn Forward

Sau đó lựa chọn RAM, CPU 

<img src=https://imgur.com/Jb3A3il.jpg>

Chọn kích thước disk cho máy ảo

<img src=https://imgur.com/ZPk5Cbi.jpg>

Tiếp theo đặt tên cho máy ảo

<img src=https://imgur.com/yvfW8YX.jpg>

Finish

Sau đó tiến hành cài đặt như bình thường

<img src=https://imgur.com/etSulfP.jpg>


