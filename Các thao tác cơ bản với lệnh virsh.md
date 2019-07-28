# Các thao tác cơ bản với lệnh virsh


## **1. Liệt kê tất cả các máy ảo đang hoạt động**

`virsh list`

## **2. Liệt kê tất cả các máy ảo**

`virsh list --all`

## **3. Chúng ta có thể xem thêm nhiều tùy chọn với lệnh**

`virsh --help`

### Các thao tác liên quan đến bật/tắt máy ảo

| Câu lệnh | Mô tả |
|----------|-------|
|help|	Hiển thị phần trợ giúp
|start|	Bật máy ảo đang stop
|shutdown|	Gửi lệnh shutdown tới máy ảo
|destroy|	Ngay lập tức đóng tất cả ứng dụng và shutdown máy ảo
|reboot|	Khởi động lại máy ảo
|domid|	Hiển thị ID máy ảo
|domuuid|	Hiển thị UUID máy ảo
|dominfo|	Hiển thị thông tin máy ảo
|domstate|	Hiển thị trạng thái máy ảo
|save|	Lưu trạng thái hiện tại của máy ảo vào file
|restore|	Restores trạng thái hiện tại của máy ảo từ file.
suspend|	Tạm dừng máy ảo
resume|	Khởi động máy ảo từ trạng thái pause
autostart|	Tự động start máy ảo khi hệ thống start
autostart –disable|	Tắt tự động start máy ảo
undefine –domain|	Thực hiện việc xóa máy ảo / sau đó thực hiện xóa file ổ đĩa

## Quản lý tài nguyên VM

| Câu lệnh | Mô tả |
|----------|-------|
setmem|	Set RAM cho máy ảo
setmaxmem|	Đặt giới hạn bộ nhớ tối đa
setvcpus|	Thay đổi số vCPU cho máy ảo. Lưu ý không support trên RHEL 5
vcpuinfo|	Hiển thị vCPU của máy ảo
vcpupin|	Kiểm soát mối quan hệ CPU ảo
domblkstat|	Hiển thị thông tin block device của máy ảo
domifstat|	Hiển thị thông tin network của máy ảo
attach-device|	Gán thiết bị vào máy ảo
attach-disk	|Gán ổ đĩa mới vào máy ảo
attach-interface|	Gán network interface mới vào máy ảo
detach-device|	Tháo thiết bị khỏi máy ảo
detach-disk|	Tháo ổ đĩa khỏi máy ảo
detach-interface|	Tháo network interface máy ảo
domxml-from-native|	Chuyển đổi cấu hình từ các lib khác sang libvirt XML (libxl )
domxml-to-native|	Chuyển đổi cấu hình libvirt XML sang các định dạng khác

## 4.  Quản lý snapshot

### Tạo snapshot:

`virsh snapshot-create-as --domain tên_máy_ảo tên_snapshot`

VD: 

<img src=https://imgur.com/v9KhN70.jpg>

### Liệt kê các snapshot

`virsh snapshot-list tên_máy_ảo`

### Hiển thị thông tin snapshot

`virsh snapshot-info --domain tên_máy_ảo --snapshotname tên_snapshot `

### Quay lại snapshot

`virsh snapshot-revert --domain tên_máy_ảo --snapshotname tên_snapshot`

### Xóa snapshot

`virsh snapshot-delete -domain tên_máy_ảo --snapshotname tên_snapshot`

<img src=https://imgur.com/yc1fgj1.jpg>

## 5. Clone máy ảo

### Cài công cụ để clone
`yum install virt-clone -y`

**Sử dụng lệnh dưới đây sẽ tự động tạo tên máy clone mới và đường dẫn đến nơi lưu trữ mới**

`virt-clone -o tên_máy_ảo --auto-clone`

VD:

<img src=https://imgur.com/wHMULoY.jpg>
