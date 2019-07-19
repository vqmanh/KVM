# Tìm hiểu file XML trên KVM

## 1. Tổng quan về XML

- XML (viết tắt từ tiếng Anh: eXtensible Markup Language, tức “Ngôn ngữ đánh dấu mở rộng” là ngôn ngữ đánh dấu với mục đích chung do W3C đề nghị, để tạo ra các ngôn ngữ đánh dấu khác, có khả năng mô tả nhiều loại dữ liệu khác nhau. 
- Mục đích chính của XML là đơn giản hóa việc chia sẻ dữ liệu giữa các hệ thống khác nhau, đặc biệt là các hệ thống được kết nối với Internet.
- VM trong KVM có hai thành phần chính đó là VM’s definition được lưu dưới dạng file XML mặc định ở thư mục /etc/libvirt/qemu và VM’s storage lưu dưới dạng file image.
- File domain XML chứa những thông tin về thành phần của máy ảo (số CPU, RAM, các thiết lập của I/O devices…)
- Libvirt dùng những thông tin này để tiến hành khởi chạy tiến trình QEMU-KVM tạo máy ảo.
- Ngoài file domain XML còn có các file XML khác để lưu thông tin network, storage... Ta có thể thấy mỗi máy ảo được tạo ra có một file xml chứa các thông tin của nó.

<img src=https://imgur.com/sU8E0Wh.jpg>

## 2. Các thành phần trong file XML

**Ta có thể dùng lệnh virsh edit tên_file để chỉnh sửa (chú ý tên file bỏ phần đuôi .xml) hoặc ta cũng có thể sử dụng vi hoặc vim để chỉnh sủa nó.**






