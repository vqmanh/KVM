# Các thao tác cơ bản với lệnh virsh
*```centos7: tên máy ảo```*

**1. Khởi động máy ảo**

`virsh start centos7`

**2. Khởi động máy ảo kết nối tới bảng điều khiển**

`virsh start centos7 --console`

**3. Dừng máy ảo**

`virsh shutdown centos7`

**4. Đặt tự động khởi động cho máy ảo**

`virsh autostart centos7`

**5. Tắt tự động khởi động máy ảo**

`virsh autostart --disable centos7`

**6. Liệt kê tất cả các máy ảo đang hoạt động**

`virsh list`

**7. Liệt kê tất cả các máy ảo**

`virsh list --all`

**8. Chúng ta có thể xem thêm nhiều tùy chọn với lệnh**

`virsh --help`