### Cài đặt NFS server và client trên Centos 7

Hướng dẫn này giải thích cách cấu hình máy chủ NFS trên CentOS 7. Network File System (NFS)) là một filesystem protocol tệp phân tán phổ biến cho phép người dùng gắn kết các thư mục từ xa trên server của họ. NFS cho phép bạn tận dụng không gian lưu trữ ở một vị trí khác và cho phép bạn ghi vào cùng một không gian từ nhiều máy chủ hoặc máy khách một cách dễ dàng. Hoạt động khá tốt cho các thư mục mà người dùng cần truy cập thường xuyên.

### Mô hình 

<img src="/img/8.jpg">

| Name              | IP  |             
|-------------------|-----|
|Controller| 192.168.239.150|
|Compute1|192.168.239.151|
|Compute2|192.168.239.152|
|Cinder|192.168.239.153|
|NFS|192.168.239.154|








































