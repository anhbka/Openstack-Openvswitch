### Openstack-Rocky

### 1. Mô hình

<img src="/img/1.jpg">

Controller : IP 192.168.239.139

Compute1 : IP 192.168.239.140

### 2. Cài đặt

Set name controller: 

`hostnamectl set-hostname controller`

`hostnamectl set-hostname compute1`

`/etc/hosts`

```sh 
192.168.239.139    controller
192.168.239.140    compute1
```

Tắt `firewall` và `selinux`:

``` sh
systemctl disable firewalld
systemctl stop firewalld
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```
Khởi động lại máy: ` init 6`


### Cài đặt Network Time Protocol (NTP)

`yum install chrony -y`

``` sh
sed -i "s/server 0.centos.pool.ntp.org iburst/server vn.pool.ntp.org iburst/g" /etc/chrony.conf

sed -i 's/server 1.centos.pool.ntp.org iburst/#server 1.centos.pool.ntp.org iburst/g' /etc/chrony.conf

sed -i 's/server 2.centos.pool.ntp.org iburst/#server 2.centos.pool.ntp.org iburst/g' /etc/chrony.conf

sed -i 's/server 3.centos.pool.ntp.org iburst/#server 3.centos.pool.ntp.org iburst/g' /etc/chrony.conf

sed -i 's/#allow 192.168.0.0\/16/allow 192.168.239.0\/24/g' /etc/chrony.conf
```
Chạy lệnh:

``` sh
systemctl enable chronyd.service
systemctl start chronyd.service
```

Kiểm tra:

`chronyc sources`

### Cài đặt repo

`yum install centos-release-openstack-queens`

`yum upgrade -y`

### Install the OpenStack client:

`yum install python-openstackclient -y`

### Install the openstack-selinux package:

`yum install openstack-selinux -y`

### SQL database

`yum install mariadb mariadb-server python2-PyMySQL -y`


`vi  /etc/my.cnf.d/openstack.cnf`

``` sh
[mysqld]
bind-address = 192.168.239.139

default-storage-engine = innodb
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8
```


































































