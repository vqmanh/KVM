# Tìm hiểu và cài đặt KVM trên CentOS7

## Mục lục
[1. KVM là gì?](#dinhnghia)

[2. Cách cài đặt KVM](#caidat)

  - [a. Chuẩn bị](#cb)
  - [b. Các bước cài đặt](#cai)

[3. Tạo và quản lí máy ảo với virt](#tao)

  - [a. Cài đặt các công cụ quản lý virt](#virt)
  - [b. Sử dụng virt-manager](#manager)
  - [c. Sử dụng virt-install](#install)

---
### <a name = "dinhnghia"> 1. KVM là gì?

KVM (viết tắt của Kernel Virtualization Machine) là công nghệ ảo hóa phần cứng. Có nghĩa là OS (hệ điều hành) chính mô phỏng phần cứng cho các OS khác để chạy trên đó.

KVM VPS không có tài nguyên dùng chung, tất cả đã được mặc định sẵn và chia sẻ. Điều đó có nghĩa là RAM của mỗi VPS KVM đã được phân bổ sẵn cho từng gói VPS để sở hữu, tận dụng hoàn toàn 100% và không bị chia sẻ ra ngoài. Đồng nghĩa với việc VPS KVM sẽ hoạt động ổn định hơn mà không bị ảnh hưởng bởi các VPS khác cùng hoạt động trên hệ thống. Tương tự điều đó với disk space, tài nguyên ổ cứng cũng đã được xác định sẵn và không bị vay mượn bởi các VPS khác.
### <a name = "caidat"> 2. Cách cài đặt KVM
#### <a name = "cb"> a. Chuẩn bị:
Một máy chạy hệ điều hành CentOS 7 với cấu hình 2 CPU, 2 GB RAM và 10 GB disk.

#### <a name = "cai"> b. Các bước cài đặt:

**Bước 1: Update, cài đặt KVM và các gói phụ trợ liên quan**

`yum update -y`

`yum install qemu-kvm libvirt bridge-utils virt-manager -y`

Trong đó:

  - *qemu-kvm*: Phần phụ trợ cho KVM.
  - *libvirt-bin*: cung cấp libvirt mà bạn cần quản lý qemu và KVM bằng libvirt.
  - *bridge-utils*: chứa một tiện ích cần thiết để tạo và quản lý các thiết bị bridge.
  - *virt-manager*: cung cấp giao diện đồ họa để quản lý máy ảo.


***Lưu ý: Nếu bạn sử dụng VMWare, hãy nhớ bật chức năng ảo hóa Intel VT-x/EPT hoặc AMD-V/RVI***


 <img src="https://i.imgur.com/te8RkiW.png">

**Bước 2: Kiểm tra hệ thống có hỗ trợ KVM hay khônng**


  `grep -e 'vmx' /proc/cpuinfo`

  *Nếu kết quả trả về là khác 0 thì CPU có hỗ trợ*

  **Bước 3: Kiểm tra để chắc chắn rằng KVM đã được cài đặt**

  `lsmod | grep kvm`

  <img src="https://i.imgur.com/wpKnkFb.jpg">

  
**Bước 4: Start dịch vụ**

  `systemctl start libvirtd`
  
  `systemctl enable libvirtd`
  

**Bước 5: Tạo, cấu hình card bridge br0**

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

**Bước 6: Restart lại card mạng**

  `/etc/init.d/network restart`

***Reboot***

### <a name = "tao"> 3. Tạo và quản lí máy ảo với virt

#### <a name = "virt"> a. Cài đặt các công cụ quản lý virt

`yum -y install libguestfs-tools libguestfs-xfs virt-top`

***Xem các file, folder trên máy ảo***

  `virt-ls -l -d centos7 /root`

  ***Đọc một file trên máy áo***
  
  `virt-cat -d centos7 /etc/passwd `

  ***Chỉnh sửa một file trên máy ảo***

  `virt-edit -d centos7 / etc / fstab `

  ***Hiển thị dung lượng disk trên máy ảo***

  `virt-edit -d centos7 / etc / fstab `

  ***Gắn đĩa cho máy ảo***

  `guestmount -d centos7 -i /media`

  ***Hiển thị trạng thái của máy ảo***

  `virt-top`


#### <a name = "manager"> Sử dụng virt-manager

***Chú ý: Nên sử dụng MobaXterm để SSH vào thì mới có thể sử dụng được virt-manager***


**Bước 1: Tiến hành download file iso hoặc images và đặt tại thư mục**

```/var/lib/libvirt/images```

**ISO**

`wget http://mirrors.nhanhoa.com/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1611.iso /var/lib/libvirt/images`


*Hoặc bạn có thể đưa trực tiếp file ISO vào thư mục /var/lib/libvirt/images với WinSCP hoặc dùng MobaXterm*

<img src=https://i.imgur.com/nFb8ZXI.jpg>

**Images**

`wget http://cloud.centos.org/centos/7/images/CentOS-7-x86_64-Azure-1703.qcow2 /var/lib/libvirt/images` 

**Bước 2: Mở virt-manager**

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

#### <a name = "install"> Sử dụng virt-install

**Bước 1: Cài virt-install và virt-viewer để hỗ trợ cài máy ảo**

`yum install virt-install virt-viewer`


**Bước 2: Tạo nơi lưu trữ máy ảo**

`mkdir -p /var/kvm/images`

**Bước 3: Tạo máy ảo**

***Hiển thị những template OS có sẵn***

`virt-builder -l `

**Tạo image cho CentOS7.5**

`virt-builder centos-7.5 --format qcow2 --size 10G -o centos75.qcow2 --root-password password`


***Tạo máy ảo với image***


`virt-install --name centos-75 --ram 2048 --disk path=/var/kvm/images/centos75.qcow2 --vcpus 2 --os-type linux --os-variant rhel7.5 --network bridge=br0 --graphics none --serial pty --console pty --boot hd --import`

***Tạo máy ảo với file ISO***

`virt-install --name=CentOS7 --file=/var/kvm/images/centos7.img --file-size=10 --nonsparse --graphics spice --vcpus=2 --ram=2048 --cdrom=/var/lib/libvirt/images/CentOS-7-x86_64-DVD-1708.iso --network bridge=br0 --os-type=linux --os-variant=generic`

<img src=https://imgur.com/hgdSqS1.jpg>

**Trong đó:**
  - –name = Đặt tên cho máy ảo
  - –file = Vị trí nơi lưu trữ máy ảo
  - –file-size = Kích thước máy ảo
  - –nonsparse = Phân bổ toàn bộ lưu trữ trong khi tạo
  - –graphics = Đi kèm với các tùy chọn sau

    - "type": Loại đồ họa. Có thể chọn "vnc" hoặc "sdl", "spice" và "none"
    - "port": nếu người dùng sử dụng vnc hoặc spice thì sẽ phải khai báo port cố định cho dịch vụ này
    - Một vài tùy chọn khác như "listen", "password"
  - –vcpu = Đặt số lượng CPU
  - –ram = Đặt số lượng RAM
  - –cdrom = Đường dẫn đến file ISO
  - –network = Lựa chọn card Bridge br0
  - –os-type = Chọn loại hệ điều hành (ví dụ như linux hoặc window). Mặc định, virt-install sẽ tự động xác định thông qua file cài đặt.
  - -os-variant=generic = Là một optional command, cho biết xa hơn về loại hệ điều hành (ví dụ như fedora8 hoặc winxp). Sử dụng --os-variant list để xem tất cả list os
  - -import = Bỏ qua phần cài đặt os cho máy ảo, dùng file image có sẵn thông qua tùy chọn --disk hoặc filesystem
  - -boot = Thiết lập những cài đặt cơ bản trước khi boot. Tùy chọn này cho phép người dùng đặt thứ tự thiết bị boot. Ví dụ: --boot cdrom,fd,hd,network,menu=on

**Sau khi chạy xong lệnh giao diện virt-viewer sẽ hiện ra**

Các bạn cài đặt như bình thường

<img src=https://imgur.com/ozxloro.jpg>


