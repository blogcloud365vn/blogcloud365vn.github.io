---
date: 2019-03-20
title: Hướng dẫn sử dụng công cụ MTR (My Traceroute)
categories:
  - Linux
description: Hướng dẫn sử dụng công cụ MTR (My Traceroute)
author: duydm
tags: [Beginer, Linux]
type: Document
---

Đối với một người quản trị hệ thống việc giám sát sự hoạt động ổn định của hệ thống là một điều cực kỳ quan trọng, các hệ thống triển khai dịch vụ online thì vấn đề được đặt lên hàng đầu đó là `network`. Để có thể giám sát được tình trạng online, các package gửi/nhận thông thường quản trị hệ thống sử dụng các command line như `ping`, `traceroute` để xem quá trình truyển tải các package trên mạng qua các điểm có ổn định hay không. Thay vì sử dụng những câu lệnh đơn lẻ và phải tập hợp các thông số rời rạc để phân tích đưa ra nhận định, bạn có thể sử dụng các tool chuyện dụng cho việc phân tích mạng. Sau đây sẽ hướng dẫn các bạn sử dụng công cụ `mtr` (my traceroute) để thực hiện đánh giá, phân tích trạng thái của network.

![](/images/img-mtr/mtr.png)

`MTR` là một công cụ bao gồm 2 chương trình `traceroute` và `ping`. Khi bạn thực hiện chạy công cụ `mtr` nó sẽ thực hiện kết nối giữa host bạn đang cài mtr và một máy chủ khác thông qua địa chỉ IP hoặc hostname bằng cách gửi các package TTLs với chỉ số thấp qua các điểm khác nhau cho tới disk, các gói tin được gửi liên tục, % tỷ lệ được tính bằng gói tin gửi đi, gói tin gửi về từ đó giúp người quản trị đưa ra nhận định chính xác về tình trạng network kết nối tới host/server cần kiểm tra.

## 1. MTR trên Linux

### 1.1. Cài đặt

Để sử dụng được công cụ mtr trên Linux bạn thực hiện cài đặt các gói mtr trên môi trường Linux

**Host mrt CentOS**

```
yum install mtr
```

**Host mrt Ubunutu**

```
apt-get install mtr
```

### 1.1.2. Thao tác sử dụng

**Kiểm tra kết nối tới domain/IP**

```
mtr [domainName/IP]
```
![](/images/img-mtr/Screenshot_1225.png)

![](/images/img-mtr/Screenshot_1222.png)

**Hiển thị địa chỉ IP và hostname tương ứng của các điểm kết nối với flag `b`**

```
mtr -b [domainName/IP]
```

![](/images/img-mtr/Screenshot_1226.png)

![](/images/img-mtr/Screenshot_1224.png)

**Giới hạn số lượng gói tin ping gửi đi với flag `c`**

```
mtr -c [n] “domainname/IP”
```

![](/images/img-mtr/Screenshot_1228.png)

![](/images/img-mtr/Screenshot_1227.png)

**Xuất kết quả ra file với flag `r`**

Thay vì hiển thị kết quả lên trên màn hình command line bạn có thể xuất ra file để lưu và theo dõi.

```
mtr –rw -c [n] “domainname/IP” >”report-name”
```

![](/images/img-mtr/Screenshot_1229.png)

**Sắp xếp lại thông số đầu ra của kết quả với flag `o`**

```
mtr -o “[Output Format]” “domainname/IP”
```

![](/images/img-mtr/Screenshot_1231.png)

![](/images/img-mtr/Screenshot_1230.png)

**Chỉnh khoảng thời gian gửi mỗi gói tin  ICMP và ECHO với flag `o`**

Mặc định khoảng thười gian liên tiếp giữa mỗi gói itn ICMP, ECHO là 1 giây bạn có thể thay đổi lại tham số này.

```
mtr -i [time-in-seconds] “domainName/IP”
```

![](/images/img-mtr/Screenshot_1232.png)

**Sử dụng TCP SYN packets hoặc UDP datagrams với flag `tcp`, `udp`**

Mặc định `mtr` sẽ gửi đi các gói tin ICMP ECHO requests bạn có thể tùy chọn sử dụng TCP SYN packets hoặc UDP datagrams.

```
mtr –tcp “domainName/IP”
```

```
mtr –udp “domainName/IP”
```
![](/images/img-mtr/Screenshot_1233.png)

**Chỉ định package size bytes với flag -s**

```
mtr –r -s [packetsize] “domainName/IP”
```

![](/images/img-mtr/Screenshot_1234.png)

**Chỉ định số hope tối đa giữa host mtr và host đích với flag `m`**

```
mtr -m [timeInSeconds] “domainName/IP”
```
![](/images/img-mtr/Screenshot_1236.png)

![](/images/img-mtr/Screenshot_1235.png)

**Xuất file kết quả ra dạng cvs với flag `csv`**

```
mtr –-csv “domainName/IP”
```
![](/images/img-mtr/Screenshot_1237.png)

**Xuất file kết quả ra dạng xml  với flag `xml`**

```
mtr -–xml “domainName/IP”
```

![](/images/img-mtr/Screenshot_1238.png)

## 2. MTR trên Windows

### 2.1. Cài đặt

**Download**

`MTR` trên windows thuộc dạng file .exe chạy không cần cài đặt lên máy. Thực hiện download <a href="https://github.com/domanhduy/ghichep/blob/master/DuyDM/Linux/scripts/winmtr_bin_0.8.zip" target="_blank"> tại đây</a>.

**Chạy chường trình MTR trên windows**

Sau khi tải về bạn tiến hành giải nén và run file `WinMTR.exe` 

![](/images/img-mtr/Screenshot_1239.png)

### 2.2. Thao tác sử dụng

**Một số option tùy biến**

![](/images/img-mtr/Screenshot_1240.png)

+ Interval (sec): Khoảng thời gian giữa mỗi package mtr gửi đi.

+ Ping size (bytes): Kích thức gói tin mtr gửi đi.

+ Max hosts in LRU list: Số hope đi qua.

+ Reslove name: Hiển thị hoặc không hiển thị hostname các hope đi qua.

**Bạn chỉ cần nhập IP/Domain và click `Start`**

![](/images/img-mtr/Screenshot_1241.png)

**Kết quả**

![](/images/img-mtr/Screenshot_1242.png)

MTR trên windows hỗ trợ export kết quả ra dạng `text`, `html`.

## 3. Đọc và phân tích kết quả**

Khi sử dụng tool `mtr` sẽ trả về kết quả bạn cần chú ý một số thông tin sau:

![](/images/img-mtr/Screenshot_1244.png)

+ Hostname: Các hope package đi qua trước khi tới được host đích cần kiểm tra.

+ Lost %: Tỷ lệ phần trăm các package bị mất khi truyền tới hope đó.

+ Sent: Số package gửi đi.

+ Recv: Số package phản hồi về.

+ Avrg: Thời gian phản hổi trung bình (ms). 

+ Best: Thời gian phản rồi nhanh nhất (ms).

+ Worst: Thời gian phản hồi lâu nhất (ms).

+ Last: Thời gian phản hồi của gói tin gần đây nhất (ms).

+ StDev: Độ lệch chuẩn.

Hy vọng bài hướng dẫn này giúp bạn biết thêm một công cụ nữa để troubleshoot hệ thống của mình.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>