---
title: CHECKMK part 1 - Tìm hiểu về checkmk
categories:
  - Monitor
description: Tìm hiểu về omd-checkmk.
author: datpt
tags: [monitoring, Linux]
type: Document
set: Gioi-thieu-checkmk
set_order: 25
---

## Lời mở đầu.
Khi càng nhiều công ty hoạt động dựa trên hệ thống mạng, việc giám sát hệ thống cũng trở nên quan trọng hơn. Các sự cố gián đoạn hệ thống mạng, máy chủ không hoạt động, các dịch vụ và ứng dụng gặp vấn đề… đều gây ảnh hưởng nghiêm trọng đến hoạt động doanh nghiệp. Tổn thất có thể lên đến hàng ngàn, thậm chí hàng triệu USD tùy theo doanh thu và lĩnh vực hoạt động. Để giảm thiểu thiệt hại đến mức tối thiểu hoặc ngăn chặn những sự cố đáng tiếc xả ra thì sẽ cần sự giúp đỡ rất nhiều từ các hệ thống giám sát. Hôm nay mình xin giới thiệu về bộ giải pháp OMD - Checkmk một giải pháp giám  sát mã nguồn mở có khả năng giám sát, cảnh báo và hiển thị các biểu đồ khá tốt đối với những hệ thống tầm trung không sẵn sàng chi  nhiều kinh phí cho việc giám sát hệ thống, mạng.

## 1. OMD - Checkmk là gì?

OMD - Open Monitoring Distribution là một dự án được phát triển từ năm 2010 bới Mathias Kettner. OMD sử dụng nhân là Nagios Core, kết hợp với các phần mềm mã nguồn mở khác để đóng gói thành một sản phẩm phục vụ cho nhu cầu giám sát, cảnh báo và hiển thị.

Dự án Check_MK được phát triển từ năm 2008 như là một plugin của Nagios Core.

Năm 2010 dự án OMD (Open Monitoring Distribution) được khởi động bởi Mathias Kettner, là sự kết hợp của Nagios, Check_MK, NagVis, PNP4Nagios, DocuWiki, ...tạo nên sự linh hoạt trong giám sát. Các distro của OMD đang là OMD-LABS và CHECK_MK RAW.

Check_MK là một phần của OMD, hiện tại đang có 2 phiên bản Check_MK là Check_MK Raw Edition (CRE) và Check_MK Enterprise Edition (CEE)

## 2. Ưu điểm trong thiết kế kiến trúc của OMD.

OMD được xây dựng từ những đóng góp của cộng đồng về những khó khăn hay khuyết điểm mà Nagios gặp phải, từ đó đưa ra quyết định cần tích hợp thêm những sản phẩm gì để cải thiện.

Việc cài đặt trở nên vô cùng đơng giản. OMD được đóng gói hoàn chỉnh trong một package, việc cài đặt và cấu hình chỉ mất khoảng 10 phút với chỉ một câu lệnh

![omd-1](/images/img-omd/omd-1.png)

Check_MK ra đời để giải quyết bài toán về hiệu năng mà Nagios gặp phải trong quá khứ.Cơ chế mới của Check_MK cho phép việc mở rộng hệ thống trở nên dễ dàng hơn, có thể giám sát nhiều hệ thống chỉ từ một máy chủ Nagios server.

Có 2 mô đun mà Check_MK sử dụng để cải thiện đáng kể hiệu năng là Livestatus và Livecheck.

- Livestatus có những thay đổi để cải thiện hiệu năng đó là:
    - Livestatus cũng sử dụng Nagios Event Broker API như NDO, nhưng nó sẽ không chủ động ghi dữ liệu ra. Thay vào đó, nó sẽ mở ra một socket để dữ liệu có thể được lấy ra theo yêu cầu.
    - Livestatus tiêu tốn ít CPU.
    - Livestatus không làm cho Disk I/O thay đổi khi truy vấn trạng thái dữ liệu.
    - Không cần cấu hình. Không cần cơ sở dữ liệu. Không cần quản lý.

- Livecheck hoạt động như thế nào để cải thiện được hiệu năng :
    - Livecheck sử dụng các helper process, các core giao tiếp với helper thông qua Unix socket (điều này không xảy ra trên file system).
    - Chỉ có một một helper program được fork thay vì toàn bộ Nagios Core.
    - Các tiến trình fork được phân tán trên tất cả các CPU thay vì chỉ một như trước.
    - Process VM size tổng chỉ khoảng 100KB.

## 3. Cài đặt check_mk trên Centos 7.

Truy cập vào link sau [đây](https://mathias-kettner.de/download.php?) để xem các phiên bản được release, ở bài này mình sẽ sử dụng phiên bản `1.5.0p13` để cài đặt.


Cài đặt `wget` và `epel-release`:

```sh
yum install -y epel-release wget
```

Tải về file cài đặt:

```sh
wget https://mathias-kettner.de/support/1.5.0p13/check-mk-raw-1.5.0p13-el7-38.x86_64.rpm
```

Sử dụng yum để cài đặt gói rpm để có thể tải được đầy đủ những dependencies:

```sh
yum install -y check-mk-raw-1.5.0p13-el7-38.x86_64.rpm
```

Đợi từ 3 đến 5 phút để hoàn thành quá trình cài đặt, sau đó tạo site mới bằng câu lệnh sau:

```sh
omd create cloud365
```

Kết quả thu được như sau:

![omd-2](/images/img-omd/omd-2.png)

Sử dụng lệnh sau để tạo mật khẩu `cmkadmin`:

```sh
htpasswd -m /omd/sites/cloud365/etc/htpasswd cmkadmin
```

- Trong đó `/omd/sites/cloud365/` là $HOME_SITE của site tạo password, ở đây là `cloud365`

Kết quả như sau:

![omd-3](/images/img-omd/omd-3.png)

Khởi động site:

```sh
omd start cloud365
```

Truy cập địa chỉ `ip-server/cloud365` để kiểm xem đã cài đặt và khởi động thành công hay chưa, nếu thành công kết quả nhận được sẽ như sau:

![omd-4](/images/img-omd/omd-4.png)

Đăng nhập với tài khoản `cmkadmin` và mật khẩu khởi tạo cho site ở bên trên, kết quả như sau:

![omd-5](/images/img-omd/omd-5.png)

Như thế là đã cài đặt thành công trên CentOS 7, ở bài viết sau mình sẽ hướng dẫn mọi người cách để thu thập các thông tin cơ bản để monitor.

## Cài đặt check_mk trên ubuntu 16.04.


Download bản cài đặt về cho Ubuntu 16.04:

```sh
wget https://mathias-kettner.de/support/1.5.0p13/check-mk-raw-1.5.0p13_0.xenial_amd64.deb
```

Cài đặt gói `gdebi` để hỗ trợ cài đặt các gói dependencies:

```sh
apt install gdebi-core -y
```

Cài đặt checkmk:

```sh
gdebi check-mk-raw-1.5.0p13_0.xenial_amd64.deb
```

Chờ từ 3 đến 5 phút để quá trình cài đặt kết thúc, tạo site mới :

```sh
omd create cloud365
```

Các bước tiếp theo thực hiện giống như cài đặt trên CentOS 7.


## Tổng kết.

Hôm nay mình đã giới thiệu về checkmk và cách để cài đặt trên 2 OS đó là CentOS 7 và Ubuntu 16.04, trong bài viết sau mình sẽ giới thiệu về cách thu thập một số thông số giám sát cơ bản. Hãy ghé thăm cloud365 để tìm được các bài viết hay hơn.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>