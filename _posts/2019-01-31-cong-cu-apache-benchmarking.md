---
date: 2019-01-31
title: "Hướng dẫn sử dụng AB Benchmarking Tool trên Ubuntu 18.04"
categories:
  - Linux
description: Hướng dẫn sử dụng AB Benchmarking Tool trên Ubuntu 18.04
author: thanhnb
tags: [Load balancer, Linux, CentOS7]
type: Document
---

## Giới thiệu
Kiểm tra, đánh giá tải là điều cần thiết trước khi trên khải sản phẩm. Và thật khó nếu sử dụng sức người để kiểm tra các kịch bản đặt ra.

Công cụ __Apache HTTP server benchmarking tool__ (ab) có thể tạo ra tải mô phỏng tới web server bằng cách gửi các request đồng thời tới máy chủ. Công cụ này được tích hợp mặc định sau khi cài dịch vụ HTTP Apache để đánh giá Web server sau khi cài đặt.

## Chuẩn bị
### Đăng ký dịch vụ máy chủ Web
Truy cập <a href="https://nhanhoa.com/may-chu/may-chu-ao-vps.html" target="_blank">Nhân hòa</a>, đăng ký 1 Máy ảo CentOS 7 với cấu hình 2 CPU, 2GB RAM - 25 GB Disk (Cấu hình gói B)

![](/images/img-ab-tool/pic4.png)


Kết quả

![](/images/img-ab-tool/pic1.png)

### Cài đặt máy chủ dịch vụ Web

Truy cập máy chủ ảo trên hệ thống nhân hòa

Cài đặt dịch vụ apache

```
yum install httpd -y

echo '<h1>Chào mừng tới Blog Cloud365</h1>' > /var/www/html/index.html

systemctl start httpd
systemctl enable httpd
```

Kết quả

![](/images/img-ab-tool/pic5.png)

### Tại máy tính cá nhân

Tại máy tính cá nhân (chạy hệ điều hành ubuntu 18.04) cài đặt các gói sau

```
sudo apt-get install apache2-utils
```

## Phần 1: Cú pháp và tùy chọn Apache HTTP server benchmarking tool

__Cú pháp đầy đủ__

```
Cấu trúc: ab [options] [http[s]://]hostname[:port]/path
```

__Hiển thị các tùy chọn__

```
ab -h

# Kết quả
thanhnb@thanhnb-pc:~$ ab -h
Usage: ab [options] [http[s]://]hostname[:port]/path
Options are:
    -n requests     Number of requests to perform
    -c concurrency  Number of multiple requests to make at a time
    -t timelimit    Seconds to max. to spend on benchmarking
                    This implies -n 50000
    -s timeout      Seconds to max. wait for each response
                    Default is 30 seconds
    -b windowsize   Size of TCP send/receive buffer, in bytes
    -B address      Address to bind to when making outgoing connections
    -p postfile     File containing data to POST. Remember also to set -T
    -u putfile      File containing data to PUT. Remember also to set -T
    -T content-type Content-type header to use for POST/PUT data, eg.
                    'application/x-www-form-urlencoded'
                    Default is 'text/plain'
    -v verbosity    How much troubleshooting info to print
    -w              Print out results in HTML tables
    -i              Use HEAD instead of GET
    -x attributes   String to insert as table attributes
    -y attributes   String to insert as tr attributes
    -z attributes   String to insert as td or th attributes
    -C attribute    Add cookie, eg. 'Apache=1234'. (repeatable)
    -H attribute    Add Arbitrary header line, eg. 'Accept-Encoding: gzip'
                    Inserted after all normal header lines. (repeatable)
    -A attribute    Add Basic WWW Authentication, the attributes
                    are a colon separated username and password.
    -P attribute    Add Basic Proxy Authentication, the attributes
                    are a colon separated username and password.
    -X proxy:port   Proxyserver and port number to use
    -V              Print version number and exit
    -k              Use HTTP KeepAlive feature
    -d              Do not show percentiles served table.
    -S              Do not show confidence estimators and warnings.
    -q              Do not show progress when doing more than 150 requests
    -l              Accept variable document length (use this for dynamic pages)
    -g filename     Output collected data to gnuplot format file.
    -e filename     Output CSV file with percentages served
    -r              Don't exit on socket receive errors.
    -m method       Method name
    -h              Display usage information (this message)
    -I              Disable TLS Server Name Indication (SNI) extension
    -Z ciphersuite  Specify SSL/TLS cipher suite (See openssl ciphers)
    -f protocol     Specify SSL/TLS protocol
                    (SSL2, TLS1, TLS1.1, TLS1.2 or ALL)
```

## Phần 2: Thực hiện bài Test đơn giản

### Cú pháp đơn giản

```
ab -n <Số lượng request> -c <Số lượng đồng thời> <IP hoặc Domain>:<port><path>
```

Các tham số cơ bản:
- `-n` - <Số lượng> Tổng số request thực hiện
- `-c` - <Số lượng thực hiện> Số lượng request thực hiện đồng thời

### Ví dụ minh họa

Thực hiện test (Thực hiện tại máy tính cá nhân)

```
ab -n 10000 -c 100 http://10.10.11.38/
```

Kết quả

```
Server Software:        Apache/2.4.6
Server Hostname:        10.10.11.38
Server Port:            80

Document Path:          /
Document Length:        42 bytes

Concurrency Level:      100
Time taken for tests:   8.030 seconds
Complete requests:      10000
Failed requests:        0
Total transferred:      3020000 bytes
HTML transferred:       420000 bytes
Requests per second:    1245.28 [#/sec] (mean)
Time per request:       80.304 [ms] (mean)
Time per request:       0.803 [ms] (mean, across all concurrent requests)
Transfer rate:          367.26 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        1   30 136.5      8    2233
Processing:     5   45  50.7     30     964
Waiting:        4   43  44.8     30     658
Total:          7   75 146.7     38    2242

Percentage of the requests served within a certain time (ms)
  50%     38
  66%     60
  75%     99
  80%    106
  90%    118
  95%    137
  98%    382
  99%   1090
 100%   2242 (longest request)
```

Giải thích ý nghĩa
- Server Software - Phiển bản hiện thời của Web Server
- Server Hostname - DNS hoặc IP cung cấp cho việc đánh giá, chỉ định port trên command line
- Server Port - Port kết nối tới, nếu không chỉ định port trên command line, giá trị mặc định là 80 cho http hoặc 443 cho https
- SSL/TLS Protocol - Giao thức sử dụng mặc định giữa client và Server, sẽ chỉ xuất hiện nếu sử dụng SSL
- Document Path - Request URI chỉ định Test
- Document Length - Độ dài của phản hồi (HTML, CSS), tính bằng byte. Nếu độ dài của phản hồi thay đổi, kết quả phản hồi được coi là lỗi
- Concurrency Level - Số lượng client đồng thời gửi request
- Time taken for tests - Tổng thời gian test, tính từ request đầu tiên tới request cuối cùng
- Complete requests - Số lượng Request thành công
- Failed requests - Số lượng Request thất bại, nếu có sẽ có mô tả về lỗi
- Non-2xx responses - Số lượng phản hồi không phải status code 200.
- Total body sent - Chỉ quan tâm khi bài test thực hiện hành động POST hoặc PUT tới server
- Total transferred - Tổng số byte nhận được từ server
- HTML transferred - Số lượng byte nhận được (Các file html), kết quả đã loại bỏ số byte trong HTTP header
- Requests per second - Tổng số request thực hiện trong 1s. Tính bằng tổng thời gian chia số lượng request hoàn thành (Time taken for tests / Complete requests)
- Time per request - Thời gian trung bình trên một request.
- Transfer rate - Tốc độ đường truyền (Downloading tại client). Tính bằng: <Tổng byte nhận>/ 1024 / <Thời gian thực hiện>
- Connection Times: Tính bằng ms
  - Connect và Waiting times: Thời gian sử dụng để mở kết nối và nhận kết quả (bit đầu tiên)
  - Processing times: Thời gian đợi server xử lý và phản hồi
  - Total time: Tổng thời gian xử lý (Connect + Processsing + Waiting)


Phân tích kết quả:
- Phiên bản hiện tại của Apache webserver là `Apache/2.4.6`
- Sẽ có 100 client đồng thời gửi request tới server (`Concurrency Level`)
- Quá trình truyển tải hết `2.8801 MB` (3020000 bytes) cho 10000 request
- Hoàn thành test mất 8.030s
- Có `1245.28` request thực hiện trong 1s (`Requests per second`)
- Băng thông sử dụng hết `367.26 Kbytes/sec` (`Transfer rate`)

### Thực hiện bài test và xuất kết quả dạng `gnuplot format`

[Xem thêm tại](http://lowrank.net/gnuplot/tics-e.html)

Bổ sung thêm tùy chọn `-g <Tên file>` vào câu lệnh test

Thực hiện test (Thực hiện tại máy tính cá nhân)

```
ab -n 10 -c 2 -g data.txt http://10.10.11.38/
```

Kết quả

```
cat data.txt

starttime	                seconds     ctime	dtime	ttime	wait
Thu Jan 31 11:56:44 2019	1548910604	1	    2	    3	    2
Thu Jan 31 11:56:44 2019	1548910604	1	    2	    3	    2
Thu Jan 31 11:56:44 2019	1548910604	1	    2	    3	    2
Thu Jan 31 11:56:44 2019	1548910604	1	    2	    3	    2
Thu Jan 31 11:56:44 2019	1548910604	1	    2	    4	    2
Thu Jan 31 11:56:44 2019	1548910604	1	    2	    4	    2
Thu Jan 31 11:56:44 2019	1548910604	1	    2	    4	    2
Thu Jan 31 11:56:44 2019	1548910604	2	    2	    4	    2
Thu Jan 31 11:56:44 2019	1548910604	2	    2	    4	    2
Thu Jan 31 11:56:44 2019	1548910604	2	    3	    5	    3
```
Lưu ý:
- `seconds`: Kiểu dữ liệu thời gian (Dạng timestamp linux, UTC) (VD: 1548910604 = Thursday, January 31, 2019 4:56:44 AM)
- `ctime`: Thời gian mở kết nối (Connection Time)
- `dtime`: Thời gian xử lý (Processing Time)
- `ttime`: Tổng thời gian (Tổng = ctime + dtime )
- `wait`: Thời gian chờ (Waiting Time)

pic time-explain

### Phần 3: Thực hiện Test với Web động
Để có kết quả sát với thực tế, ta sẽ dùng AB Tool kiểm tra 1 web động (ở đây ta sẽ dùng flask python) để đối sánh kết quả

### Cài đặt flask

__Thực hiện tại máy chủ ảo__

Cài đặt `python3.6` và `virtualenv`

```
yum install https://centos7.iuscommunity.org/ius-release.rpm -y
yum install python-devel -y
yum install python36-devel -y
yum install python36 -y
yum install python36u-mod_wsgi -y

yum install python-pip -y
yum install python36u-pip -y
pip3.6 install virtualenv
```

Cài đặt `flask`

```
mkdir webserver_demo && cd webserver_demo
virtualenv env
source env/bin/activate


pip install Flask==0.10.1
pip freeze > requirements.txt


echo 'from flask import Flask
app = Flask(__name__)


@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run(host="0.0.0.0")' > app.py
```

Chạy web server
```
python app.py
```

Kết quả

![](/images/img-ab-tool/pic6.png)

### Thực hiện bài test và đánh giá

Thực hiện Test với request cơ bản (Thực hiện tại máy tính cá nhân)
```
ab -n 100 -c 1 http://10.10.11.38:5000/
```

Kết quả bài test đơn giản
![](/images/img-ab-tool/pic2.png)

Test với request bổ sung thêm các tùy chọn mở rộng (Thực hiện tại máy tính cá nhân)
```
ab -l -r -n 100 -c 1 -k -H "Accept-Encoding: gzip, deflate"  http://10.10.11.38:5000/
```

Kết quả bài test nâng cao

![](/images/img-ab-tool/pic3.png)

Giải thích tham số:

- `-l` - Cho phép nhiều độ dài file khác nhau (Dùng web động)
- `-r` - Mặc định ab sẽ tự thoát khi nhận lỗi phản hồi từ webserver, với tham số `-r` ab sẽ thông báo lỗi nhưng bài test vẫn tiếp tục thực hiện
- `-H` - Thêm các thông số vào header request (Tham số giúp request giống với thực tế hơn)

Sẽ có sự khác biệt giữa test với các request đơn giản và các request với thêm các tùy chọn, từ đó ta sẽ có thểm các số liệu để đánh giá với hệ thống thực tế trước khi triển khai


Tham số tùy chọn khác:

- `-v 2` - Trong trường hợp thực hiện bài test gặp lỗi, sử dụng tham số này để thấy header HTTP phản hồi lại từ server

## Tổng kết
Các kết quả có được khi đánh giá hệ thống Web theo AB Tool có thể không phản ánh chính xác hiệu năng của server. HTTP chỉ là 1 phần rất nhỏ khi muốn đánh giá hiệu năng hệ thống, hiệu năng của hệ thống Web còn bao gồm rất nhiều thành phần khác như các `templating engine`, `database`. Cuối cùng, công cụ sẽ cung cấp các thông số để so sánh, đánh giá ban đầu

Các công cụ đánh giá hệ thống Web khác:
- httperf
- weighttp
- httpress
- siege
- JMeter
- tourbus

## Nguồn

https://www.digitalocean.com/community/tutorials/how-to-use-apachebench-to-do-load-testing-on-an-ubuntu-13-10-vps

http://httpd.apache.org/docs/2.2/programs/ab.html

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>