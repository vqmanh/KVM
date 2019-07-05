# Các thao tác cơ bản với lệnh virsh
*```centos7: tên máy ảo```*

**Khởi động máy ảo**

`virsh start centos7`

**Khởi động máy ảo kết nối tới bảng điều khiển**

`virsh start centos7 --console`

**Dừng máy ảo**

`virsh shutdown centos7`

**Đặt tự động khởi động cho máy ảo**

`virsh autostart centos7`

**Tắt tự động khởi động máy ảo**

`virsh autostart --disable centos7`

**Liệt kê tất cả các máy ảo đang hoạt động**

`virsh list`

**Liệt kê tất cả các máy ảo**

`virsh list --all`

**Chúng ta có thể xem thêm nhiều tùy chọn với lệnh**

`virsh --help`