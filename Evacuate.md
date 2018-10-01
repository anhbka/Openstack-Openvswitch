### Tìm hiểu về Evacuate


### EVACUATE INSTANCES

Nếu bạn muốn di chuyển một instance từ một node dead hoặc tắt đến một compute node mới trong cùng một môi trường (ví dụ, vì server cần được hoán đổi), bạn có thể di tản nó bằng cách dùng nova evacuate .

Việc evacuate chỉ hữu ích nếu các instance disks trên sử dụng share storage. Nếu không, các disk sẽ không thể truy cập được và không thể truy cập được bằng node compute mới.

Một instance chỉ có thể evacuate khi server tắt nếu không thì sẽ không thể evacuate thành công.

Một số kĩ thuật khác được dùng để vận chuyển máy ảo:

* Tạo ra một bản copy của máy ảo cho mục đích backup hoặc copy nó tới một môi trường/hệ thống mới, sử dụng snapshot (nova image-create)

* Di chuyển máy ảo ở trạng thái static tới host trên cùng 1 môi trường/hệ thống, sử dụng cold migrate (nova migrate)

* Di chuyển máy ảo ở trạng thái đang chạy tới host mới trên cùng 1 môi trường/hệ thống, sử dụng live migrate (nova live-migration

### Workflow khi evacuate máy ảo:

<img src="/img/2.png">

1.	User gửi yêu cầu evacuate máy ảo tới Nova API

2.	Nova API tiếp nhận yêu cầu, kiểm tra các tùy chọn

3.	Nova API gửi yêu cầu lấy instance object tới Nova Compute API, sau khi lấy được, nó sẽ gọi tới phương thức evacuate.

4.	Nova Compute API đặt trạng thái cho máy ảo là REBUILDING và record lại action của máy ảo là REBUILD vào database.

5.	Nova Compute API yêu cầu nova compute manager tạo lại máy ảo.

6.	Nova compute check lại tài nguyên

7.	Nova Compute đặt trạng thái instance là REBUILDING trong database thông qua nova conductor

8.	Nếu capacity ok thì nó sẽ bắt đầu yêu cầu neutron set up network cho máy ảo. Trường hợp capacity không ok thì những thông tin về network và bdm (block device mapping) sẽ bị xóa bởi nova-compute driver.

9.	Nova compute lấy network info từ network service

10.	Nova compute lấy tất cả các thông tin của bdm thông qua nova conductor

11.	Sau khi có được thông tin, nó sẽ yêu cầu volume service gỡ volume ra khỏi máy ảo cũ.

12.	Nova compute sau đó thiết lập trạng thái REBUILD_BLOCK_DEVICE_MAPPING cho máy ảo thông qua nova conductor

13.	Nova compute yêu cầu thiết lập bdm cho máy ảo mới.

14.	Trạng thái của máy ảo bắt đầu được chuyển thành REBUILD_SPAWNING.

15.	Cùng lúc đó, nova compute yêu cầu nova compute driver spawn máy ảo với những thông tin đã có sẵn

16.	Trạng thái của máy ảo chuyển thành ACTIVE
































