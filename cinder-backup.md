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


### Node Cinder

Cài đặt packages:

`yum install nfs-utils -y`

Tạo thư mục chia sẻ:

`mkdir /var/nfsshare`

Thay đổi quyền của thư mục:

``` sh
chmod -R 755 /var/nfsshare
chown nfsnobody:nfsnobody /var/nfsshare
```

Khởi động các dịch vụ:

``` sh
systemctl enable rpcbind
systemctl enable nfs-server
systemctl enable nfs-lock
systemctl enable nfs-idmap
systemctl start rpcbind
systemctl start nfs-server
systemctl start nfs-lock
systemctl start nfs-idmap
```

Cấu hình quyền truy cập:

`vi /etc/exports`

Thêm vào 2 dòng :

``` sh
/var/nfsshare    192.168.239.154(rw,sync,no_root_squash,no_all_squash)
/home            192.168.239.154(rw,sync,no_root_squash,no_all_squash)
```

Note: IP 192.168.239.154 là IP của NFS client nếu bạn muốn tất cả IP trong dải 192.168.239.0 đều truy cập được thì có thể thay đổi bằng dấu "*" thay vì điền IP vào.

Khởi động  NFS service:

`systemctl restart nfs-server`

Nếu chưa tắt firewalld thì sử dụng lệnh sau:

``` sh
firewall-cmd --permanent --zone=public --add-service=nfs
firewall-cmd --permanent --zone=public --add-service=mountd
firewall-cmd --permanent --zone=public --add-service=rpc-bind
firewall-cmd --reload
```

### NFS client

Cài đặt packages:

`yum install nfs-utils -y`

Tạo thư mục chia sẻ NFS:

``` sh
mkdir -p /mnt/nfs/home
mkdir -p /mnt/nfs/var/nfsshare
```

Chỉnh sửa :

`vi /etc/exports`

`/mnt/nfs/var/nfsshare 192.168.239.0/24(rw,no_root_squash)`

`mount -t nfs 192.168.239.153:/home /mnt/nfs/home/

`mount -t nfs 192.168.239.153:/var/nfsshare /mnt/nfs/var/nfsshare/`

Dùng lệnh `df -kh` để kiểm tra xem thư mục đã được mount chưa.

<img src="/img/9.jpg">






























