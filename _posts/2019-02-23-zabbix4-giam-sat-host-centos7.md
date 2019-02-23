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


