---
date: 2019-05-11
title: "High Availability - Hướng dẫn triển khai Rabbit Cluster trên CentOS 7"
categories:
  - Linux
description: High Availability - Hướng dẫn triển khai Rabbit Cluster trên CentOS 7
author: thanhnb
tags: [Cluster, High Availability, Linux, CentOS7]
type: Document
set: high-availability
set_order: 9
---

## Tổng quan

RabbitMQ là một message broker (message-oriented middleware) sử dụng giao thức AMQP - Advanced Message Queue Protocol (Đây là giao thức phổ biến, thực tế rabbitmq hỗ trợ nhiều giao thức). RabbitMQ được lập trình bằng ngôn ngữ Erlang. RabbitMQ cung cấp cho lập trình viên một phương tiện trung gian để giao tiếp giữa nhiều thành phần trong một hệ thống lớn ( VD: Openstack RabbitMQ ). RabbitMQ sẽ nhận message đến từ các thành phần khác nhau trong hệ thống, lưu trữ chúng an toàn trước khi đẩy đến đích.

Trong bài này mình sẽ hướng dẫn các bạn triển khai hệ thống RabbitMQ theo mô hình Cluster

## Mô hình

```
HOSTNAME    rabbitmq94
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: access network (10.10.10.94)
Network     eth1: replicate network (10.10.11.94)

HOSTNAME    rabbitmq95
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: access network (10.10.10.95)
Network     eth1: replicate network (10.10.11.95)

HOSTNAME    rabbitmq96
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: access network (10.10.10.96)
Network     eth1: replicate network (10.10.11.96)
```

## Cài đặt

### Chuẩn bị

#### Tại rabbitmq94

Cấu hình hostname

```
hostnamectl set-hostname rabbitmq94

echo "10.10.10.94 rabbitmq94" >> /etc/hosts
echo "10.10.10.95 rabbitmq95" >> /etc/hosts
echo "10.10.10.96 rabbitmq96" >> /etc/hosts
```

Cài đặt môi trường
```
yum -y install epel-release
yum update -y
yum -y install erlang socat wget
```

Cấu hình network
```
echo "Setup IP eth0"
nmcli c modify eth0 ipv4.addresses 10.10.10.94/24
nmcli c modify eth0 ipv4.gateway 10.10.10.1
nmcli c modify eth0 ipv4.dns 8.8.8.8
nmcli c modify eth0 ipv4.method manual
nmcli con mod eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli c modify eth1 ipv4.addresses 10.10.11.94/24
nmcli c modify eth1 ipv4.method manual
nmcli con mod eth1 connection.autoconnect yes
```

Tắt Firewall, SELinux

```
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld
init 6
```

#### Tại rabbitmq95

Cấu hình hostname

```
hostnamectl set-hostname rabbitmq95

echo "10.10.10.94 rabbitmq94" >> /etc/hosts
echo "10.10.10.95 rabbitmq95" >> /etc/hosts
echo "10.10.10.96 rabbitmq96" >> /etc/hosts
```

Cài đặt môi trường
```
yum -y install epel-release
yum update -y
yum -y install erlang socat wget
```

Cấu hình network
```
echo "Setup IP eth0"
nmcli c modify eth0 ipv4.addresses 10.10.10.95/24
nmcli c modify eth0 ipv4.gateway 10.10.10.1
nmcli c modify eth0 ipv4.dns 8.8.8.8
nmcli c modify eth0 ipv4.method manual
nmcli con mod eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli c modify eth1 ipv4.addresses 10.10.11.95/24
nmcli c modify eth1 ipv4.method manual
nmcli con mod eth1 connection.autoconnect yes
```

Tắt Firewall, SELinux

```
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld
init 6
```

#### Tại rabbitmq96

Cấu hình hostname

```
hostnamectl set-hostname rabbitmq96

echo "10.10.10.94 rabbitmq94" >> /etc/hosts
echo "10.10.10.95 rabbitmq95" >> /etc/hosts
echo "10.10.10.96 rabbitmq96" >> /etc/hosts
```

Cài đặt môi trường
```
yum -y install epel-release
yum update -y
yum -y install erlang socat wget
```

Cấu hình network
```
echo "Setup IP eth0"
nmcli c modify eth0 ipv4.addresses 10.10.10.96/24
nmcli c modify eth0 ipv4.gateway 10.10.10.1
nmcli c modify eth0 ipv4.dns 8.8.8.8
nmcli c modify eth0 ipv4.method manual
nmcli con mod eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli c modify eth1 ipv4.addresses 10.10.11.96/24
nmcli c modify eth1 ipv4.method manual
nmcli con mod eth1 connection.autoconnect yes
```

Tắt Firewall, SELinux

```
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld
init 6
```

### Cài đặt RabbitMQ

Lưu ý: Cài đặt trên tất cả các node

Cài đặt gói RabbitMQ
```
wget https://www.rabbitmq.com/releases/rabbitmq-server/v3.6.10/rabbitmq-server-3.6.10-1.el7.noarch.rpm
rpm --import https://www.rabbitmq.com/rabbitmq-release-signing-key.asc
rpm -Uvh rabbitmq-server-3.6.10-1.el7.noarch.rpm
```

Khởi động RabbitMQ Server
```
systemctl start rabbitmq-server
systemctl enable rabbitmq-server
systemctl status rabbitmq-server
```

Kích hoạt plugin rabbit management
```
rabbitmq-plugins enable rabbitmq_management
chown -R rabbitmq:rabbitmq /var/lib/rabbitmq/
```
### Cấu hình Rabbit Cluster

#### Tại rabbitmq94

Kiểm tra trạng thái node

```
[root@rabbitmq94 ~]# sudo rabbitmqctl status|grep rabbit
Status of node rabbit@rabbitmq94
     [{rabbit,"RabbitMQ","3.6.10"},
      {rabbit_common,
          "Modules shared by rabbitmq-server and rabbitmq-erlang-client",
```

Tạo User cho App (Portal), phân quyền

```
rabbitmqctl add_user admin Cloud365a@123
rabbitmqctl set_user_tags admin administrator
rabbitmqctl add_vhost admin_vhost
rabbitmqctl set_permissions -p admin_vhost admin ".*" ".*" ".*"
```

Copy file /var/lib/rabbitmq/.erlang.cookie từ rabbitmq94 sang các node còn lại. (Có nhập password)
```
scp /var/lib/rabbitmq/.erlang.cookie root@rabbitmq95:/var/lib/rabbitmq/.erlang.cookie

scp /var/lib/rabbitmq/.erlang.cookie root@rabbitmq96:/var/lib/rabbitmq/.erlang.cookie
```

Cấu hình policy HA Rabbit Cluster
```
rabbitmqctl -p admin_vhost set_policy ha-all '^(?!amq\.).*' '{"ha-mode": "all"}'
```

Kiểm tra trạng thái cluster

```
rabbitmqctl cluster_status
```

Khởi chạy App
```
[root@rabbitmq94 ~]# rabbitmqctl start_app
Starting node rabbit@rabbitmq94
```

Kiểm tra trạng thái cluster

```
[root@rabbitmq94 ~]# rabbitmqctl cluster_status

Cluster status of node rabbit@rabbitmq94
[{nodes,[{disc,[rabbit@rabbitmq94]}]},
 {running_nodes,[rabbit@rabbitmq94]},
 {cluster_name,<<"rabbit@rabbitmq94">>},
 {partitions,[]},
 {alarms,[{rabbit@rabbitmq94,[]}]}]
```

#### Tại rabbitmq95

Phân quyền file /var/lib/rabbitmq/.erlang.cookie
```
chown rabbitmq:rabbitmq /var/lib/rabbitmq/.erlang.cookie
chmod 400 /var/lib/rabbitmq/.erlang.cookie
```

Khởi động lại dịch vụ
```
systemctl restart rabbitmq-server.service
```

Join cluster rabbitmq94
```
rabbitmqctl stop_app
rabbitmqctl join_cluster rabbit@rabbitmq94
rabbitmqctl start_app
```

#### Tại rabbitmq96

Phân quyền file /var/lib/rabbitmq/.erlang.cookie
```
chown rabbitmq:rabbitmq /var/lib/rabbitmq/.erlang.cookie
chmod 400 /var/lib/rabbitmq/.erlang.cookie
```

Khởi động lại dịch vụ
```
systemctl restart rabbitmq-server.service
```

Join cluster rabbitmq94
```
rabbitmqctl stop_app
rabbitmqctl join_cluster rabbit@rabbitmq94
rabbitmqctl start_app
```

### Kiểm tra trên tất cả các node

Tại node rabbitmq94
```
[root@rabbitmq94 ~]# rabbitmqctl cluster_status
Cluster status of node rabbit@rabbitmq94
[{nodes,[{disc,[rabbit@rabbitmq94,rabbit@rabbitmq95,rabbit@rabbitmq96]}]},
 {running_nodes,[rabbit@rabbitmq96,rabbit@rabbitmq95,rabbit@rabbitmq94]},
 {cluster_name,<<"rabbit@rabbitmq94">>},
 {partitions,[]},
 {alarms,[{rabbit@rabbitmq96,[]},
          {rabbit@rabbitmq95,[]},
          {rabbit@rabbitmq94,[]}]}]
```

Tại node rabbitmq95
```
[root@rabbitmq95 ~]# rabbitmqctl cluster_status
Cluster status of node rabbit@rabbitmq95
[{nodes,[{disc,[rabbit@rabbitmq94,rabbit@rabbitmq95,rabbit@rabbitmq96]}]},
 {running_nodes,[rabbit@rabbitmq96,rabbit@rabbitmq94,rabbit@rabbitmq95]},
 {cluster_name,<<"rabbit@rabbitmq94">>},
 {partitions,[]},
 {alarms,[{rabbit@rabbitmq96,[]},
          {rabbit@rabbitmq94,[]},
          {rabbit@rabbitmq95,[]}]}]
```

Tại node rabbitmq96
```
[root@rabbitmq96 ~]# rabbitmqctl cluster_status
Cluster status of node rabbit@rabbitmq96
[{nodes,[{disc,[rabbit@rabbitmq94,rabbit@rabbitmq95,rabbit@rabbitmq96]}]},
 {running_nodes,[rabbit@rabbitmq94,rabbit@rabbitmq95,rabbit@rabbitmq96]},
 {cluster_name,<<"rabbit@rabbitmq94">>},
 {partitions,[]},
 {alarms,[{rabbit@rabbitmq94,[]},
          {rabbit@rabbitmq95,[]},
          {rabbit@rabbitmq96,[]}]}]
```

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
