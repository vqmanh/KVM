# Tìm hiểu và cài đặt KVM trên CentOS7

## Mục lục
[1. KVM là gì?](#dinhnghia)

[2. Cách cài đặt KVM](#caidat)

  - [A. Chuẩn bị](#cb)
  - [B. Các bước cài đặt](#cai)

[3. Tạo và quản lí máy ảo với virt](#tao)

  - [A. Cài đặt các công cụ quản lý virt](#virt)
  - [B. Sử dụng virt-manager](#manager)
  - [C. Sử dụng virt-install](#install)
  - [D. Sử dụng Webvirtmgr](#web)

---
### <a name = "dinhnghia"> 1. KVM là gì?

KVM (viết tắt của Kernel Virtualization Machine) là công nghệ ảo hóa phần cứng. Có nghĩa là OS (hệ điều hành) chính mô phỏng phần cứng cho các OS khác để chạy trên đó.

KVM VPS không có tài nguyên dùng chung, tất cả đã được mặc định sẵn và chia sẻ. Điều đó có nghĩa là RAM của mỗi VPS KVM đã được phân bổ sẵn cho từng gói VPS để sở hữu, tận dụng hoàn toàn 100% và không bị chia sẻ ra ngoài. Đồng nghĩa với việc VPS KVM sẽ hoạt động ổn định hơn mà không bị ảnh hưởng bởi các VPS khác cùng hoạt động trên hệ thống. Tương tự điều đó với disk space, tài nguyên ổ cứng cũng đã được xác định sẵn và không bị vay mượn bởi các VPS khác.
### <a name = "caidat"> 2. Cách cài đặt KVM
#### <a name = "cb"> A. Chuẩn bị:
Một máy chạy hệ điều hành CentOS 7 với cấu hình 2 CPU, 2 GB RAM và 10 GB disk.

#### <a name = "cai"> B. Các bước cài đặt:

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

#### <a name = "virt"> A. Cài đặt các công cụ quản lý virt

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


#### <a name = "manager"> B. Sử dụng virt-manager

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

#### <a name = "install"> C. Sử dụng virt-install

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


#### <a name = "web"> D. Sử dụng Webvirtmgr

*Webvirtmgr là một trong những công cụ quản lý máy ảo KVM. Nó có chức năng gần giống với virt-manager khi có thể kết nối đến nhiều host KVM để có thể quản lý tập trung các VM trên các máy đó. Nhưng nó có một ưu điểm đặc biệt hơn so với virt-manager đó là với virt-manager đó là ta chỉ có thể làm việc tại máy cài virt-manager. Còn với Webvirtmgr ta có thể làm việc với các VM ở bất kỳ đâu có internet*

**Chuẩn bị môi trường**

  - Một máy cài đặt hệ điều hành CentOS 7 có cấu hình tối thiểu 1 CPU, 1 GB RAM và có 1 card mạng.

  - Ít nhất một máy CentOS 7 đã cài đặt KVM để kiểm tra lại webvirtmgr đã hoạt động.

  **Mô hình**

  <img src=https://imgur.com/fEQ9jQA.jpg>

  *Chú ý: Vì đây là môi trường lap nên tôi dùng địa chỉ IP private nên chỉ có thể quản truy cập vào Webvirtmgr trong môi trường mạng LAN. Nếu bạn muốn truy cập vào Webvirtmgr ở bất kỳ đâu Server của bạn cần có IP public.*

**Bước 1: Trong môi trường lab, tiến hành tắt firewalld và selinux trên server cài Webvirtmgr**

`systemctl stop firewalld`

`setenforce 0`

**Bước 2: Cài một số gói cần thiết**

`yum install epel-release`

`yum -y install git python-pip libvirt-python libxml2-python python-websockify supervisor nginx cyrus-sasl-md5`

`yum -y install gcc python-devel`

`pip install --upgrade pip`

`pip install numpy`

**Bước 3: Clone Webvirtmgr từ GitHub và cài đặt**

`git clone git://github.com/retspen/webvirtmgr.git`

`cd webvirtmgr`

`pip install -r requirements.txt`

***Sau đó ta chạy lệnh để thiết lập môi trường Django và tạo ra một tài khoản để đăng nhập Webvirtmgr***

`./manage.py syncdb`

***Nó sẽ hiển thị để cho ta nhập một số thông số cần thiết như username, địa chỉ E-mail, và password***


<img src=https://imgur.com/XXAnz1d.jpg>

***Sau đó tiếp tục chạy lệnh***

`./manage.py collectstatic`

***Move thư mục webvirtmgr và trong thư mục /var/www***

`mkdir /var/www`

`cd ../`

`mv webvirtmgr /var/www/`

**Bước 4: Cài đặt nginx**

***Mở file webvirtmgr (ban đầu file này chưa tồn tại câu lệnh bên dưới cũng tiến hành tạo file)***

`vi /etc/nginx/conf.d/webvirtmgr.conf`

***Thêm vào file những dòng sau***

```
server {
listen 80 default_server;
 
server_name $hostname;
access_log /var/log/nginx/webvirtmgr_access_log;
 
location /static/ {
root /var/www/webvirtmgr/webvirtmgr; # or /srv instead of /var
expires max;
}
 
location / {
proxy_pass http://127.0.0.1:8000;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-for $proxy_add_x_forwarded_for;
proxy_set_header Host $host:$server_port;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_connect_timeout 600;
proxy_read_timeout 600;
proxy_send_timeout 600;
client_max_body_size 1024M; # Set higher depending on your needs
}
}
```

***Vào file /etc/nginx/nginx.conf***

`vi /etc/nginx/nginx.conf`

***Comment lại những dòng sau bằng cách thêm dấu "#" vào đầu dòng:***

<img src=https://imgur.com/jIjJi2l.jpg>

***Restart và enable nginx***

`service nginx restart`

`systemctl enable nginx`

***Cho phép Webvirtmgr khởi động cùng hệ thống***

`/usr/sbin/setsebool httpd_can_network_connect true`

`chkconfig supervisord on`

**Bước 5: Setup Supervisor**

***Cấp quyền cho người dùng***

`chown -R nginx:nginx /var/www/webvirtmgr`

***Mở file webvirtmgr.ini (file này ban đầu cũng chưa tồn tại)***

`vi /etc/supervisord.d/webvirtmgr.ini`

***Thêm vào những dòng sau:***

```
[program:webvirtmgr]
command=/usr/bin/python /var/www/webvirtmgr/manage.py run_gunicorn -c /var/www/webvirtmgr/conf/gunicorn.conf.py
directory=/var/www/webvirtmgr
autostart=true
autorestart=true
logfile=/var/log/supervisor/webvirtmgr.log
log_stderr=true
user=nginx
 
[program:webvirtmgr-console]
command=/usr/bin/python /var/www/webvirtmgr/console/webvirtmgr-console
directory=/var/www/webvirtmgr
autostart=true
autorestart=true
stdout_logfile=/var/log/supervisor/webvirtmgr-console.log
redirect_stderr=true
user=nginx
```

***Restart supervisord***

systemctl restart supervisord

***Đến đây bạn đã cài đặt xong Webvirtmgr. Bạn mở trình duyệt và truy cập vào địa chỉ của server cài webvirtmgr. Kết quả như sau:***

<img src=https://imgur.com/g07DGPS.jpg>

**Bước 6: Chuyển sang Host KVM**

***Để Webvirtmgr có thể kết nối đến Host KVM và quản lý được các VM trong host KVM ta cần cấu hình một số thông tin sau trên host KVM***

***Tắt firewalld***

`systemctl stop firewalld`

***Muốn xác thực để tiến hành kết nối qemu+tcp bạn thực hiện câu lệnh sau***

***Tiến hành cài đặt gói cyrus-sasl-md5***

`yum install cyrus-sasl-md5`

`sed -i 's/mech_list: gssapi/#mech_list: gssapi/g' /etc/sasl2/libvirt.conf`

`sed -i 's/#sasldb_path: /etc/libvirt/passwd.db/#sasldb_path: /etc/libvirt/passwd.db/g' /etc/sasl2/libvirt.conf`

`sed -i 's/#auth_tcp = "sasl"/auth_tcp = "sasl"/g' /etc/libvirt/libvirtd.conf`

`sed -i 's/#user = "root"/user = "root"/g' /etc/libvirt/qemu.conf`

`sed -i 's/#group = "root"/group = "root"/g' /etc/libvirt/qemu.conf`

`sed -i 's/#LIBVIRTD_ARGS="--listen"/LIBVIRTD_ARGS="--listen"/g' /etc/sysconfig/libvirtd`

***Restart lại libvirtd***

`systemctl restart libvirtd`

***Sau đó ta cần tạo user để xác thực cho kết nối qemu+tcp***

`saslpasswd2 -a libvirt username`

***Show các user***

`sasldblistusers2 -f /etc/libvirt/passwd.db`

***Để xóa user***

`saslpasswd2 -a libvirt -d username`


**Sử dụng WebvirtMgr**

**Bước 1: Ta dùng tài khoản ta tạo ở bên trên để đăng nhập vào. Để kết nối đến máy KVM click và Add connection. Nhập IP của HostKVM.**

<img src=https://imgur.com/iTyBQ7v.jpg>

***Kết nối thành công sẽ hiện lên giao diện như sau***

<img src=https://imgur.com/pJo89BW.jpg>

**Bước 2: Click vào kết nối đó chọn Storages sau đó là New storages để tạo vị trí lưu các file khi tạo máy ảo**

<img src=https://imgur.com/OOAyQ56.jpg>

***Chọn thư mục chứa disk của VM***

<img src=https://imgur.com/E0Til9Q.jpg>

***Chọn thư mục lưu file XML của VM***

<img src=https://imgur.com/HfRwbzg.jpg>

**Bước 4: Thiết lập mạng cho các VM bằng cách click vào Networks**

<img src=https://imgur.com/a9Fh9VT.jpg>

**Bước 5: Để tạo VM trước tiên ta cần tạo cho VM đó 1 file image. Để tạo file ta vào Storage chọn vị trí lưu file image trong các thư mục ta vừa thêm ở bên trên sau đó chọn Add image. Sau đó chọn tên image, định dạng và dung lượng**

<img src=https://imgur.com/r0yE6HZ.jpg>


**Bước 6: Chọn New instance => Custom instance để tạo VM**

<img src=https://imgur.com/sMsKAyw.jpg>

***Chú ý các thông số tên VM, số CPU, dung lượng RAM, HDD để chỉ ra file image của VM trỏ đến file vừa tạo, network để chọn mạng cho VM => Create***

<img src=https://imgur.com/72pVvaZ.jpg>

**Bước 7: Chọn hệ điều hành cho VM bằng cách chỉ ra file ISO => Connect**

<img src=https://imgur.com/Xcoo5dp.jpg>

**Bước 8: Chọn Power => Start**

<img src=https://imgur.com/hHI2NIV.jpg>

***Sau đó, chọn Access => Console để hiển thị giao diện VM***

<img src=https://imgur.com/XDsqEgR.jpg>