---
date: 2019-02-20
title: Hướng dẫn cài đặt zabbix agent 4.0
categories:
  - Monitor
description: Hướng dẫn cài đặt zabbix agent lên host cần monitor chạy trên các hệ điều hành Centos, Ubuntu, Linux.
author: duydm
tags: [Zabbix]
type: Document
---

Sau khi triển khai <a href="https://blog.cloud365.vn/monitor/cai-dat-zabbix-4-lts-tren-centos7/" target="_blank">zabbix server</a> để có thể giám sát được các host client khác phải cài đặt zabbix-agent lên client, tùy theo cơ chế lựa chọn (active check, passive check, SSH check...) cho việc nhận metric từ các host. Ở bài hướng dẫn này giới thiệu cho bạn các bước để cài đặt zabbix-agent 4.0 lên host client trên hệ điều hành khác nhau (Windows, CentOS, Ubuntu).

### Mục lục

[1. Mô hình triển khai](#mohinh)<br>
[2. IP Planning](#planning)<br>
[3. Zabbix-agent CentOS](#centos)<br>
[4. Zabbix-agent Ubuntu](#ubuntu)<br>

<a name="mohinh"></a>
## 1. Mô hình triển khai

![](/images/img-agent-zabbix/topo-agent.png)

<a name="planning"></a>
## 2. IP Planning

![](/images/img-agent-zabbix/Screenshot_978.png)

<a name="centos"></a>
## 3. Zabbix-agent CentOS

+ Cài đặt zabbix-agent

**Truy cập host CentOS `10.10.10.118`**

![](/images/img-agent-zabbix/Screenshot_980.png)

**Thêm zabbix agent repository**

CentOS 7 / RHEL 7

```
rpm -ivh https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
```

CentOS 6 / RHEL 6

```
rpm -ivh https://repo.zabbix.com/zabbix/4.0/rhel/6/x86_64/zabbix-release-4.0-1.el6.noarch.rpm
```

**cài đặt**

```
yum install -y zabbix-agent
```

**Check zabbix-agent**

```
zabbix_agentd -V
```
![](/images/img-agent-zabbix/Screenshot_981.png)

+ Cấu hình zabbix-agent

Coppy file config gốc của zabbix-agent, nhập địa chỉ IP của zabbix server để client kết nối tới.

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

![](/images/img-agent-zabbix/Screenshot_982.png)

Như vậy `zabbix client` CentOS đã sẵn sàng gửi metric về `zabbix server`.

<a name="ubuntu"></a>
## 4. Zabbix-agent Ubuntu
















---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>