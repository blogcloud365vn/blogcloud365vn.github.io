---
title: CHECKMK part 2 - Hướng dẫn cài checkmk agent giám sát hệ thống Linux.
categories:
  - Monitor
description: Tìm hiểu về omd-checkmk.
author: datpt
tags: [monitoring, Linux]
type: Document
set: Gioi-thieu-checkmk
set_order: 24
---

## Lời mở đầu.
Ở bài trước mình đã hướng dẫn mọi người cài đặt checkmk server ở trên CentOS 7 và Ubuntu, ở bài này mình sẽ hướng dẫn mọi người cài đặt agent lên máy chủ cần giám sát để thu thập những metrics về checkmk servers
## Chuẩn bị.

- Một máy chủ đã được cài đặt checkmk, nếu chưa có thì cài theo [part 1](https://blog.cloud365.vn/monitor/check_mk-part1-Tong-quan-ve-checkmk-va-vai-dat/)

- Một hoặc nhiều máy chủ client để đẩy metrics về checkmk.

## 2. Cài đặt agent trên CentOS 7.

Đầu tiên, chúng ta vào Web UI để tải `Agent` cho client. Ở giao diện Web, chúng ta kéo xuống phần `WATO - Configuration`, chọn tiếp `Monitoring Agent`:

![omd-6](/images/img-omd/omd-6.png)

Ở đây chúng ta có 3 loại file cài đặt đó là :

- *.deb: Dành cho các host sử dụng DEBIAN.

- *.rpm: Dành cho các host sử dụng RHEL.

- *.msi: Dành cho các host sử dụng MS Windows.

Ở đây, agent là CentOS 7 thì chúng ta sẽ chọn *.rpm: Dành cho các host sử dụng RHEL.

![omd-7](/images/img-omd/omd-7.png)

Coppy địa chỉ đường dẫn và dùng wget để tải file cài đặt về :

```sh
yum install wget -y
wget http://ip_checkmk/cloud365/check_mk/agents/check-mk-agent-1.5.0p13-1.noarch.rpm
```

Cài đặt `xinetd`:

```sh
yum install xinetd -y
```

Khởi động lại dịch vụ `xinetd` và cho phép khởi động cùng hệ thống :

```sh
systemctl start xinetd
systemctl enable xinetd
```

Cài đặt `agent` bằng câu lệnh :

```sh
rpm -ivh check-mk-agent-1.5.0p13-1.noarch.rpm
```

Sửa file cấu hình `xinetd` của checkmk tại `/etc/xinetd.d/check_mk` :

![omd-8](/images/img-omd/omd-8.png)

Khởi động lại `xinetd`:

```sh
systemctl restart xinetd
```

Thêm host trên checkmk server, tại `WATO - Configuration` chọn `host`:

![omd-9](/images/img-omd/omd-9.png)

Chọn `Create new host`:

![omd-10](/images/img-omd/omd-10.png)

Điền các thông tin cần thiết :

![0md-11](/images/img-omd/omd-11.png)

Sau đó chọn `Save & test` để kiểm tra:

![omd-12](/images/img-omd/omd-12.png)

Kết quả thu được như sau :

![omd-13](/images/img-omd/omd-13.png)

Chọn `Save & exit` để quay trở lại màn trước:

![omd-14](/images/img-omd/omd-14.png)

Sau đó chọn `Save & go to Services` để kiểm tra những dịch vụ và những thông số vật lý được giám sát thông qua agent, tại đây chọn `Fix all missing` để khi lưu lại thì các dịch vụ sẽ lên luôn các thông số :

![omd-15](/images/img-omd/omd-15.png)

Sau các bước làm như trên thì checkmk sẽ có những thay đổi , chúng ta chọn `Changes` để xem những thay đổi đó :

![omd-16](/images/img-omd/omd-16.png)

Sau đó lưu lại để hoàn thành thêm `host` mới :

![omd-17](/images/img-omd/omd-17.png)

Kiểm tra lại host vừa mới thêm vào, tại `Views`, chọn `Hosts` rồi sau đó vào `All host`:

![omd-18](/images/img-omd/omd-18.png)


## 3. Cài đặt agent trên Ubuntu.

Đối với Ubuntu chúng ta sẽ sử dụng file `*.deb` để cài đặt agent.

Đầu tiên, chúng ta vào Web UI để tải `Agent` cho client. Ở giao diện Web, chúng ta kéo xuống phần `WATO - Configuration`, chọn tiếp `Monitoring Agent`:

![omd-6](/images/img-omd/omd-6.png)

Tìm đến file cài đặt có đuôi `.deb` coppy link của file để download về:

![omd-19](/images/img-omd/omd-19.png)

Thực hiện tải về :

```sh
wget http://ip_checkmk/cloud365/check_mk/agents/check-mk-agent_1.5.0p13-1_all.deb
```

Cài đặt `agent` bằng lệnh sau:

```sh
dpkg -i check-mk-agent_1.5.0p13-1_all.deb
```

Các bước tiếp làm tương tự như trên CentOS 7.

## Tổng kết.

Ở bài viết này mình đã hướng dẫn mọi người thêm agent cho CentOS và Ubuntu để giám sát cho client, ở bài viết sau mình sẽ giới thiệu về active check trong checkmk, là một cách khác để cấu hình giám sát dịch vụ.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>