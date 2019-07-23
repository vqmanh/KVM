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

***Trong file xml này chứa rất nhiều thông số nhưng ở đây tôi chỉ đề cập đến một số thông số đáng chú ý***

<img src=https://imgur.com/6u8J8uG.jpg>

- name: Tên máy ảo, chỉ bao gồm kí tự chữ và số và không được trùng với những máy ảo đang chạy.
- uuid: Mã nhận dạng quốc tế duy nhất cho máy ảo.
- memory: Lượng ram của máy ảo
- vcpu: Số cpu máy ảo được cài đặt

***Ở mục devices có thể cho ta biết được vị trí lưu của file images***

<img src=https://imgur.com/USoa3Qh.jpg>

- Vị trí lưu máy ảo này là: /var/lib/libvirt/images/centos7.0.qcow2

***Ở mục interface sẽ cho ta biết được địa chỉ MAC và card đang sử dụng***

*Các bạn có thể thêm card mạng bằng các sửa file XML*

<img src=https://imgur.com/z56sruv.jpg>



Ở dòng đầu tiên của cấu hình card mạng ta thấy dòng `interface type='bridge'` Ở dòng này chúng ta để là network nếu chúng ta sử dụng mô hình NAT hoặc host-only và để là bridge nếu sử dụng mô hình bridge

Ở dòng `mac address` nếu ta thêm card mạng thì ta không cần đặt mà hệ thống sẽ tự gen cho ta. Sau khi add thêm địa chỉ mac sẽ xuất hiện.

Chú ý đến các vị trí gạch đỏ. Ở dòng source ta để là `source network` nếu phần `interface type` ta để là `network` và `source bridge` nếu `interface type` là `bridge` Sau đó nếu mà `network `thì chỉ ra mạng ảo kết nối tới. Còn nếu để `bridge` thì chỉ ra tên của switch ảo.

Ở card mạng đầu tiên ta cần thêm `multifunction='on'` vào vị trí như ở trên để cho phép sử dụng nhiều PCI. Và các card ta cần thay đổi đánh số thứ tự từ 0 đến hết cho các card ở mục function như tôi đánh dấu ở trên để thay đổi chỉ số PCI cho các card.

Sau khi sửa đổi file xml ta cần chạy lệnh `virsh define tên_file`để máy ảo có thể cập nhật thay đổi đó.
*VD: virsh define centos7.0.xml*

## 3. Dump file xml

**Chúng ta sử dụng lệnh:**

`virsh dumpxml tên_file_xml > tên.xml`

*Lưu ý: tên_file_xml bỏ đuôi `xml`*

*VD: virsh dumpxml centos7.0 > cent7.xml*

<img src=https://imgur.com/RZIQpaR.jpg>

Sau khi dump xong, chúng ta sẽ vào file dump ra và sửa cấu hình:

- Sửa `name` tên máy mới

- Xóa bỏ `uuid`

- Xóa `mac address`

- Sửa `source file` đến nơi lưu trữ mới

Sau đó sử dụng lệnh 
`virsh define tên_file_xml_mới `

<img src=https://imgur.com/bk0yNGq.jpg>

Sau đó vào virt-manager rồi chạy máy ảo như bình thường.

