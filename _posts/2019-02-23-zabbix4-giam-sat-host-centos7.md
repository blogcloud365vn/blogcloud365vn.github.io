---
date: 2019-02-23
title: Zabbix 4.0 - Giám sát host zabbix client (CentOS)
categories:
  - Monitor
description: Hướng dẫn cài đặt cấu hình giám sát host zabbix client chạy hệ điều hành CentOS.
author: duydm
tags: [Zabbix]
type: Document
---

Sau khi triển khai <a href="https://blog.cloud365.vn/monitor/cai-dat-zabbix-4-lts-tren-centos7/" target="_blank">zabbix server</a> để có thể giám sát được các thông số (RAM, CPU, Disk...) của client ta phải cài đặt zabbix agent, cấu hình và add host cần giám sát lên zabbix server. Ở bài hướng dẫn này giới thiệu cho các bạn các bước để giám sát một zabbix client chạy hệ điều hành <a href="https://blog.cloud365.vn/linux/huong-dan-cai-dat-centos7/" target="_blank">CentOS</a>.

[1. Chuẩn bị](#chuanbi)<br>
[2. Cài đặt, cấu hình zabbix agent](#setup)<br>
[3. Add host zabbix agent lên zabbix server](#host)<br>


<a name="chuanbi"></a>
## 1. Chuẩn bị

**Mô hình triển khai**

![](/images/img-zabbix3-mon-centos/topo-zabbix6.png)

**IP planning**

![](/images/img-zabbix3-mon-centos/Screenshot_1013.png)

<a name="setup"></a>
## 2. Cài đặt, cấu hình zabbix agent

### 2.1. Cài đặt zabbix agent

**Truy cập host CentOS `10.10.10.119`**

![](/images/img-zabbix3-mon-centos/Screenshot_1001.png)

**Thêm zabbix agent repository**

CentOS 7 / RHEL 7

```
rpm -ivh https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
```

CentOS 6 / RHEL 6

```
rpm -ivh https://repo.zabbix.com/zabbix/4.0/rhel/6/x86_64/zabbix-release-4.0-1.el6.noarch.rpm
```

**Cài đặt**

```
yum install -y zabbix-agent
```

**Check zabbix-agent**

```
zabbix_agentd -V
```
![](/images/img-zabbix3-mon-centos/Screenshot_981.png)

### 2.2. Cấu hình zabbix agent

Khi cài đặt zabbix agent lên CentoS file cấu hình zabbix agent được đặt ở `/etc/zabbix/zabbix_agentd.conf`. Thực hiện các câu lệnh dưới cấu hình để mở port `10050`, chỉ định `IP zabbix server`.<br>
Sau mỗi thay đổi config zabbix agent bạn phải khởi đông lại và kiểm tra trạng thái service `zabbix-agent`.

```
cp /etc/zabbix/zabbix_agentd.conf /etc/zabbix/zabbix_agentd.conf.bk
sed -i 's/Server=127.0.0.1/Server=10.10.10.115/g' /etc/zabbix/zabbix_agentd.conf
sed -i 's/# ListenPort=10050/ListenPort=10050/g' /etc/zabbix/zabbix_agentd.conf
sed -i 's/ServerActive=127.0.0.1/ServerActive=10.10.10.115/g' /etc/zabbix/zabbix_agentd.conf
systemctl enable zabbix-agent
systemctl start zabbix-agent
systemctl restart zabbix-agent
systemctl status zabbix-agent
```
**Lưu ý**: Nếu host của bạn mở firewall thì phải mởi port 10050 để zabbix client giao tiếp với zabbix server.s

```
firewall-cmd --add-port=10050/tcp --permanent 
firewall-cmd --reload 
```

<a name="host"></a>
## 3. Add host zabbix agent lên zabbix server

### 3.1. Truy cập zabbix server

![](/images/img-zabbix3-mon-centos/Screenshot_1002.png)

### 3.2. Add host

Trên web dashboard của zabbix server click `Configuration -> Hosts -> Create Host -> Host`

![](/images/img-zabbix3-mon-centos/Screenshot_1003.png)

**Nhập thông tin host client**

```
Host name:
Group:
Agent interfaces:
```
![](/images/img-zabbix3-mon-centos/Screenshot_1006.png)

**Lựa chọn template**

Chuyển sang tab `Temaplates` -> `Select`

![](/images/img-zabbix3-mon-centos/Screenshot_1007.png)

Lựa chọn `Template` phù hợp với client của bạn.

Click `Add`

![](/images/img-zabbix3-mon-centos/Screenshot_1008.png)

**Add host thành công**

![](/images/img-zabbix3-mon-centos/Screenshot_1009.png)

Chờ một lúc để client kết nối tới zabbix server. Khi biểu tượng zabbix agent đổi màu xanh là ta đã add host thành công.

![](/images/img-zabbix3-mon-centos/Screenshot_1010.png)

**Kiểm tra thông số monitor**

Click `Monitoring -> Lastest data -> Lựa chọn host -> Apply`

![](/images/img-zabbix3-mon-centos/Screenshot_1011.png)



