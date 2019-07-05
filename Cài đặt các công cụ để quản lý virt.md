# Cài đặt các công cụ để quản lý virt

**1. Cài đặt công cụ virt**

`yum -y install libguestfs-tools libguestfs-xfs virt-top`

**2. Tạo máy ảo với virt**

***Hiển thị những template OS có sẵn***

`virt-builder -l `

**Tạo image cho CentOS7.5**

`export LIBGUESTFS_BACKEND=direct`

`virt-builder centos-7.5 --format qcow2 --size 20G -o centos75.qcow2 --root-password password`


***Tạo nơi lưu trữ máy ảo***

`mkdir -p /var/kvm/images`

***Tạo máy ảo với image***

`virt-install --name centos-75 --ram 4096 --disk path=/var/kvm/images/centos75.qcow2 --vcpus 2 --os-type linux --os-variant rhel7.5 --network bridge=br0 --graphics none --serial pty --console pty --boot hd --import`

***Tạo máy ảo với file ISO***

`virt-install --name=CentOS7 --file=/var/kvm/images/centos7.img --file-size=20 --nonsparse --graphics spice --vcpus=2 --ram=2048 --cdrom=/var/lib/libvirt/images/CentOS-7-x86_64-DVD-1708.iso --network bridge=br0 --os-type=linux --os-variant=generic`

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

  **3. Xem các file, folder trên máy ảo**

  `virt-ls -l -d centos7 /root`

  **4. Đọc một file trên máy áo**
  
  `virt-cat -d centos7 /etc/passwd `

  **5. Chỉnh sửa một file trên máy ảo**

  `virt-edit -d centos7 / etc / fstab `

  **6. Hiển thị dung lượng disk trên máy ảo**

  `virt-edit -d centos7 / etc / fstab `

  **7. Gắn đĩa cho máy ảo**

  `guestmount -d centos7 -i /media`

  **8. Hiển thị trạng thái của máy ảo**

  `virt-top`
