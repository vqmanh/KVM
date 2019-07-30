# Sử dụng Guestfish để đặt lại mật khẩu root đã quên cho máy ảo ở định dạng qcow2 trên KVM

## Chuẩn bị bài LAB:

- Trên môi trường VMware, sử dụng một máy ảo Centos7 đã cài đặt KVM.
- Tạo một máy ảo centos7.0 với định dạng qcow2

## Tiến hành

**Bước 1: Cài đặt Guestfish**

`yum install libguestfs-tools -y`

**Bước 2: Shutdown máy ảo**

<img src=https://imgur.com/ZfdA47y.jpg>


`virsh shutdown tên_máy_ảo`

**Bước 3: Đặt lại, thay đổi mật khẩu gốc bằng cách sử dụng Guestfish**

*Lưu ý: đầu tiên tạo mật khẩu tài khoản người dùng root mới bằng cách gõ lệnh sau rồi sử dụng mật khẩu đã mã hóa để thay đổi*

`openssl passwd -1 pass_mới`



<img src=https://imgur.com/mWZBzON.jpg>

***Sau đó chạy lệnh sau, đối với máy ảo centos7***

`guestfish --rw -a /var/lib/libvirt/images/centos7.0.qcow2`

<img src=https://imgur.com/a6OyIN7.jpg>


***Tiếp theo sử dụng các lệnh sau***

````
><fs> launch 
><fs> list-filesystems #liệt kê các phân vùng
/dev/sda1: xfs
/dev/centos/root: xfs
/dev/centos/swap: swap
><fs> mount /dev/centos/root / 
><fs> vi /etc/shadow #chỉnh sửa file shadow

````

<img src=https://imgur.com/7M3NXpb.jpg>

VD: Tương tự đối với máy ảo ubuntu

<img src=https://imgur.com/Def94nG.jpg>

***lưu ý lựa chọn mount vào phân vùng của hệ thống có chứa thư mục /etc***

***Sau khi chạy xong lệnh vi /etc/shadow, sửa pass mã hóa ở phần root bằng pass mã hóa mới từ lệnh openssl ở bên trên rồi lưu lại***

<img src=https://imgur.com/qys5fRr.jpg>

***Sau đó thoát khỏi Guestfish***

`><fs> quit`

**Bước 4: Mở máy ảo với virt-manager rồi nhập pass mới**



