# LINUX BRIDGE

## Mục lục

[A. Tổng quan về Linux Bridge](#A)

- [A1. Giới thiệu về Linux Bridge](#A1)
- [A2. Cấu trúc hệ thống sử dụng Linux bridge](#A2)

[B. Tìm hiểu Linux Bridge](#B)

- [B1. Cấu trúc và các thành phần](#B1)
- [B2. Các tính năng của Linux bridge](#B2)

[C. Quản lý Linux bridge](#C)

- [C1. Cài đặt công cụ quản lý Linux Bridge](#C1)
- [C2. Một số câu lệnh cơ bản với Linux bridge](#C2)

[D. Một số khái niệm liên quan](#D)

- [D1. Port](#D1)

- [D2. Uplink port](#D2)

- [D3. Tap interface](#D3)


[E. Lab cơ bản](#E)

- [E1. Chuẩn bị](#E1)

- [E2. Triển khai](#E2)

-----------------------------------------------

<a name = "A"></a>
## A. Tổng quan về Linux Bridge

<a name = "A1"></a>
### A1. Giới thiệu

-	**Linux Bridge** là một soft switch –  1 trong 3 công nghệ cung cấp switch ảo trong hệ thống Linux (bên cạnh macvlan và OpenvSwitch), giải quyết vấn đề ảo hóa network bên trong các máy vật lý. 

-	Bản chất, linux bridge sẽ tạo ra các switch layer 2 kết nối các máy ảo (VM) để các VM đó giao tiếp được với nhau và có thể kết nối được ra mạng ngoài. Linux bridge thường sử dụng kết hợp với hệ thống ảo hóa KVM-QEMU.

-	Linux Bridge thật ra chính là một switch ảo và được sử dụng với ảo hóa KVM/QEMU. Nó là 1 module trong nhân kernel. Sử dụng câu lệnh `brctl` để quản lý .

-   KVM được biết đến là một cơ sở hạn tầng ảo hóa cho nhân Linux. KVM cũng cung cấp các mô hình mạng trong việc ảo hóa network. Các mô hình bao gồm:

    - NAT
    - Host-only
    - Linux-bridge

***Lưu ý: Linux bridge được dùng kết hợp với các card ethernet của máy host. Không sử dụng với card wireless.***

<a name = "A2"></a>

### A2. Cấu trúc

**Mô hình**

<img src=https://imgur.com/M6FdBwJ.jpg>

**Khái niệm về physical port và virtual port:**

- Virtual Computing Device: Thường được biết đến như là máy ảo VM chạy trong host server

- Virtual NIC (vNIC): máy ảo VM có virtual network adapters(vNIC) mà đóng vai trò là NIC cho máy ảo.

- Physical swtich port: Là port sử dụng cho Ethernet switch, cổng vật lý xác định bởi các port RJ45. Một port RJ45 kết nối tới port trên NIC của máy host.

- Virtual swtich port: là port ảo tồn tại trên virtual switch. Cả virtual NIC (vNIC) và virtual port đều là phần mềm, nó liên kết với virtual cable kết nối vNIC

<a name = "B"></a>
## B. Tìm hiểu Linux Bridge

<a name = "B1"></a>
### B1. Cấu trúc và các thành phần

<img src=https://imgur.com/cxztaxR.png>

Một số khái niệm liên quan tới linux bridge:

-	Port: tương đương với port của switch thật

-	Bridge: tương đương với switch layer 2

-	Tap: hay tap interface có thể hiểu là giao diện mạng để các VM kết nối với bridge cho linux bridge tạo ra (nó nằm trong nhân kernel, hoạt động ở lớp 2 của mô hình OSI)

-	fd: forward data - chuyển tiếp dữ liệu từ máy ảo tới bridge.

### B2. Các tính năng của Linux bridge

- **STP**: Spanning Tree Protocol - giao thức chống loop gói tin trong mạng.

-	**VLAN**: chia switch (do linux bridge tạo ra) thành các mạng LAN ảo, cô lập traffic giữa các VM trên các VLAN khác nhau của cùng một switch.

-	**FDB**: chuyển tiếp các gói tin theo database để nâng cao hiệu năng switch.

<a name = "C"></a>
## C. Quản lý Linux bridge
<a name = "C1"></a>
### C1. Cài đặt công cụ quản lý Linux Bridge

Linux bridge được hỗ trợ từ version nhân kernel từ 2.4 trở lên. Để sử dụng và quản lý các tính năng của linux birdge, cần cài đặt gói bridge-utilities (dùng các câu lệnh brctl để sử dụng linux bridge). Cài đặt dùng lệnh như sau:

`sudo apt-get install bridge-ultils -y`

<a name = "C2"></a>
### C2. Một số câu lệnh cơ bản với Linux bridge

**Tạo một Bridge**

`brctl addbr <tên bridge>`

*VD: brctl addbr vqmanh*

**Xóa một Bridge**

`brctl delbr <tên bridge>`

*VD: brctl delbr vqmanh*

**Gán port cho bridge**

`brctl addif <tên bridge> <tên port>`

*VD: brctl addif vqmanh ens33*

**Xóa port trên bridge**

`brctl delif <tên bridge> <tên port>`

*VD: brctl delif vqmanh ens33*

**Hiển thị danh sách các MAC trong FDB**

`brctl showmacs <bridge>`

*VD: brctl showmacs vqmanh*

**Bật / tắt STP**

`brctl stp <bridge> <on/off>`

*VD: brctl stp vqmanh on*

**Câu lệnh để gán thông số priority cho port. Port nào có chỉ số priority cao hơn thì khi máy ảo gắn bridge đó vào thì nó sẽ nhận được IP của port có priority cao hơn**

`brctl setportprio <tên bridge> <tên port> <chỉ số priority>`

**Để kiểm tra**

`brctl show`

***Lưu ý : Các ảnh hưởng của câu lệnh chỉ là tạm thời cho đến khi máy host khởi động lại, sau khi khởi động lại, toàn bộ thông tin sẽ bị mất.***

<a name = "D"></a>
## D. Một số khái niệm liên quan

<a name = "D1"></a>
### D1. Port

- Trong networking, khái niệm port đại diện cho điểm vào ra của dữ liệu trên máy tính hoặc các thiết bị mạng. Port có thể là khái niệm phần mềm hoặc phần cứng. Software port là khái niệm tồn tại trong hệ điều hành. Chúng thường là các điểm vào ra cho các lưu lượng của ứng dụng. Tức là khái niệm port mức logic. Ví dụ: port 80 trên server liên kết với Web server và truyền các lưu lượng HTTP.

- Hardware port (port khái niệm phần cứng): là các điểm kết nối lưu lượng ở mức khái niệm vật lý trên các thiết bị mạng như switch, router, máy tính, … ví dụ: router với cổng kết nối RJ45 (L2/Ethernet) kết nối tới máy tính của bạn.

- Physical switch port: Thông thường chúng ta hay sử dụng các switch L2/ethernet với các cổng RJ45. Một đầu connector RJ45 kết nối port trên switch tới các port trên NIC của máy tính.

- Virtual switch port: giống như các physical switch port mà tổn tại như một phần mềm trên switch ảo. cả virtual NIC và virtual port đều duy trì bởi phần mềm, được kết nối với nhau thông qua virtual cable.


<a name = "D2"></a>
### D2. Uplink port

<img src=https://imgur.com/OwNAR5l.png>

- Uplink port là khái niệm chỉ điểm vào ra của lưu lượng trong một switch ra các mạng bên ngoài. Nó sẽ là nơi tập trung tất cả các lưu lượng trên switch nếu muốn ra mạng ngoài.

- Khái niệm virtual uplink switch port được hiểu có chức năng tương đương, là điểm để các lưu lượng trên các máy guest ảo đi ra ngoài máy host thật, hoặc ra mạng ngoài. Khi thêm một interface trên máy thật vào bridge (tạo mạng bridging với interface máy thật và đi ra ngoài), thì interface trên máy thật chính là virtual uplink port.

<a name = "D3"></a>
### D3. Tap interface

- Ethernet port trên máy ảo VM (mô phỏng pNIC) thường gọi là vNIC (Virtual NIC). Virtual port được mô phỏng với sự hỗ trợ của KVM/QEMU.

- Port trên máy ảo VM chỉ có thể xử lý các frame Ethernet. Trong môi trường thực tế (không ảo hóa) interface NIC vật lý sẽ nhận và xử lý các khung Ethernet. Nó sẽ bóc lớp header và chuyển tiếp payload (thường là gói tin IP) tới lên cho hệ điều hành. Tuy nhiên, với môi trường ảo hóa, nó sẽ không làm việc vì các virtual NIC sẽ mong đợi các khung Ethernet.

- Tap interface là một khái niệm về phần mềm được sử dụng để nói với Linux bridge là chuyến tiếp frame Ethernet vào nó. Hay nói cách khác, máy ảo kết nối tới tap interface sẽ có thể nhận được các khung frame Ethernet thô. Và do đó, máy ảo VM có thể tiếp tục được mô phỏng như là một máy vật lý ở trong mạng.

- Nói chung, tap interface là một port trên switch dùng để kết nối với các máy ảo VM.

<a name = "E"></a>
## E. Lab cơ bản trên môi trường VMware

<a name = "E1"></a>
### E1. Chuẩn bị

**Mô hình bài lab**

<img src=https://imgur.com/M9nAwDV.jpg>


- Một máy ảo với card ens33 thuộc dải 66.0.0.0/24, cài HDH CentOS7

- Máy ảo đã cài đặt sẵn các công cụ để quản lý và tạo máy ảo KVM.

- Nội dung bài lab: Tạo một switch ảo br0 và gán interface ens33 vào switch đó. Trên Host KVM tạo 2 máy ảo: vm0 và vm1. Cả 2 vm cùng gắn vào tap interface của switch br0. Ping kiểm tra kết nối.

*Các bạn có thể xem lại bài tạo máy ảo trên KVM tại [đây](https://github.com/vqmanh/KVM/blob/master/T%C3%ACm%20hi%E1%BB%83u%20KVM.md)*

<a name = "E2"></a>
### E2. Triển khai

**Bước 1: Tạo switch ảo br0**

`brctl addbr br0`

`nmcli c add type bridge autoconnect yes con-name br0 ifname br0`

***Lưu ý:***

- *Khi tạo bridge bằng command brctl addbr thì bridge là non-persistent bridge (tức là sẽ không có hiệu lực khi hệ thống khởi động lại).*

- *Khi cấu hình bằng câu lệnh brctl, các ảnh hưởng của nó sẽ biến mất sau khi khởi động lại hệ thống host server. Để lưu lại thông tin cấu hình trên bridge và khởi động lại cùng hệ thống thì nên lưu lại cấu hình vào file (Ghi vào file, khi boot lại hệ thống, thông tin trong file cũng được cấu hình lại. Những thông tin được lưu dưới dạng file, thì luôn khởi động cùng hệ thống - nên có thể coi là vĩnh viễn - trừ khi tự tay stop lại dịch vụ.)*



**Bước 2: Cấu hình**

`vi /etc/sysconfig/network-scripts/ifcfg-br0`

```
TYPE=Bridge  
NAME=br0  
DEVICE=br0  
ONBOOT=yes  
BOOTPROTO=none   
IPADDR=66.0.0.199  
GATEWAY=66.0.0.1  
DNS=8.8.8.8  
NETMASK=255.255.255.0
```
***Sửa lại card ens33***

```
BRIDGE=br0  
NAME=ens33  
DEVICE=ens33  
ONBOOT=yes  
BOOTPROTO=none  
```

**Bước 3: Gán card ens33 vào switch br0**

`brctl addif br0 ens33`

`brctl stp br0 on` *#enable tính năng STP nếu cần*

<img src=https://imgur.com/RFf9g9n.jpg>

*Ban đầu, khi mới được tạo, bridge sẽ có một địa chỉ MAC mặc định ban đầu. Khi thêm một NIC của host vào thì MAC của bridge sẽ là MAC của NIC luôn. Khi del hết các NIC của host trên bridge thì MAC của bridge sẽ về là 00:00:00:00:00:00 và chờ khi nào có NIC khác add vào thì sẽ lấy MAC của NIC đó.* 

**Bước 4: Restart lại card mạng**

`/etc/init.d/network restart`

*Khi khởi động lại, hệ thông sẽ đọc file cấu hình, và cấp địa chỉ cho interface br0 (đại điện cho bridge br0) thông qua liên kết giữa ens33 và mạng 66.0.0.0/24. Và các máy VM kết nối tới bridge, lấy chung dải mạng với bridge thông qua liên kết uplink qua ens33 và có thể liên lạc với mạng bên ngoài.*

**Bước 5: Sau khi tạo 2 máy ảo như mô hình. Chúng ta sẽ kiểm tra với lệnh `brctl show`**

<img src=https://imgur.com/yKJMe4A.jpg>

*Như các bạn đã thấy cả 2 vm cùng gắn vào tap interface của switch br0. Trên switch br0 sẽ tự sinh ra 2 port ảo vnet0 và vnet1 của 2 máy ảo vm0 và vm1 *

***Sau đó, chúng ta sẽ kiểm tra trên 2 vm xem địa chỉ MAC có khớp với 2 card ảo hay không.***

***Trên máy ảo vm0 ta thấy địa chỉ MAC của card eth0 chính là địa chỉ MAC của port vnet0***

<img src=https://imgur.com/I37oi7F.jpg>

***Tương tự với máy ảo vm1***

<img src=https://imgur.com/btoE2n6.jpg>


