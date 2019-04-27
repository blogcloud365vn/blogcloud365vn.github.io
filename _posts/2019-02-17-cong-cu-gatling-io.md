---
date: 2019-02-17
title: "Hướng dẫn sử dụng Gatling.IO Tool trên Ubuntu 18.04"
categories:
  - Linux
description: Hướng dẫn sử dụng Gatling.IO Tool trên Ubuntu 18.04
author: thanhnb
tags: [Gatling, Load balancer, Linux, CentOS7]
type: Document
set: performance-testing
set_order: 17
---

## Giới thiệu

Gatling là công cụ kiểm tra tải thông minh, cao cấp, dể dàng sử dụng. Gatling hỗ trợ rất tốt giao thức HTTP, ngoài ra gatling còn hỗ trợ để tạo tải trên nhiều giao thức khác (WebSockets, JMS, MQTT, ...).

Điểm khác biệt giữa Gatling.io công cụ test thông thường khác là gatling giải lập được các người dùng ảo, thực hiện các thao tác như trên trình duyết thông thường. Còn đối với các công cụ thông thường như `ab tool` sử dụng rất tốt khi test tải trên các URL đơn thuần nhưng không thể giải lập được hành vi người dùng. Đồng thời các người dùng giả lập được gatling tạo ra dưới dạng `message`, khác vào các công cụ khác sử dụng `thread` để tạo người dùng ảo, điều này khiến gatling tạo ra tải tốt hơn.

## Chuẩn bị

### Mô hình

![](/images/img-cong-cu-gatling-io/galing-mohinh.png)

## Các thuật ngữ quan trọng
### `Simulation`
Kịch bản giải lập thông qua `Gatling.io`. Chuỗi các thao tác mô phỏng thao tác người dùng (truy cập trang chủ, đăng nhập, đăng xuất).

### `Session`
Session của user mô phỏng, sử dụng trong quá trình test tải.

### `Feeders`

Nều các kịch bản yêu cầu dữ liệu đầu vào (import dữ liệu vào database, login vào nhiều user khác nhau) thì `Feeder` sẽ hỗ trợ đọc dữ liệu từ các file, từ json data hay từ excel và dùng dữ liệu có được truyền vào các thao tác mô phỏng.

### `Checks`

Trong quá trình thực hiện test, gatling sẽ tạo ra request ảo và nhận lại các response. Gatling sẽ đánh giá các `respone` dựa trên `Checks`. Với các request HTTP, `Checks` sẽ coi các status code là `2xx`, `3xx` coi là thành công. Ngoài ra `Checks` còn được sử dụng để bắt các sự kiện xảy ra trong quá trình test, lưu nó vào session (CSRF token chẳng hạn)

### `Reports`
Kết quả trả lại sau quá trình thực hiện test trên `Gatling`. Kết quả được Gatling xử lý thành các biểu đồ thân thiện, tổng quan.

## Cài đặt Gatling.io

Trong bài tôi sẽ sử dụng hệ điều hành Ubuntu 18.04 để cài `Gatling tool`.

Để chạy được `Gatling.io`, ta cần cài đặt môi trường JDK 8 (Java SE Development Kit 8)

Lưu ý: Gatling.io chỉ tương thích với phiên bản JDK 8

```
apt install openjdk-8-jdk -y
```

Tải gói `Gatling.io`
```
wget https://repo1.maven.org/maven2/io/gatling/highcharts/gatling-charts-highcharts-bundle/3.0.3/gatling-charts-highcharts-bundle-3.0.3-bundle.zip
```

Với phiên bản `bundle` (`gatling-charts-highcharts-bundle-3.0.3-bundle.zip`) ta chỉ cần giải nén là có thể sử dụng được

```
unzip gatling-charts-highcharts-bundle-3.0.3-bundle.zip
```

## Cấu trúc thư mục
`bin`: Chứa các script thực hiện `Gatling` và `Recorder`

`conf`: Chứa cấu hình cho `Gatling` như `Log, Report, ..`

`lib`: Chứa thư viên sử dụng bởi Gatling

`user-files`: Chứa dữ liệu người dùng
- `simulations`: Chứa các kịch bản test (Scale file).
- `data`: Chứa các dữ liệu mẫu (Feeder files)
- `bodies`: Chứa template cho Request ảo

`results`: Chứa kết quả sau những lần thực hiện test

### Cài đặt django web server đơn giản

> Server chạy mô trường CentOS 7 với IP 10.10.10.86

Cài đặt mỗi trường
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

Cài đặt web đơn giản
```
cd ~/
mkdir django-demo
cd django-demo

virtualenv env
source env/bin/activate
pip install django==2.1.1

django-admin startproject project
cd project/
python manage.py migrate
```

Tạo tài khoản Admin
```
$ python manage.py createsuperuser
Username (leave blank to use 'root'): admin
Email address: thanh@test.com
Password: Cloud365a@123
Password (again): Cloud365a@123
Superuser created successfully.
```

Chạy server web
```
$ python manage.py runserver 10.10.10.86:80
Performing system checks...

System check identified no issues (0 silenced).
February 14, 2019 - 09:55:24
Django version 2.1.1, using settings 'project.settings'
Starting development server at http://10.10.10.86:80/
Quit the server with CONTROL-C.
```

## Hướng dẫn sử dụng `Gatling Recorder`

Để sử dụng Gatling đơn giản nhất, tôi sẽ sử dụng `Gatling Recorder`. `Gatling Recorder` là bộ thu thập tháo tác người dùng, đơn giản nó sẽ quay lại các thao tác thực hiện trên `brower`, sau đó chuyển hóa các `thao tác` thành Gatling script (`.scala` script). Sau khi có Gatling script, chúng ta sẽ chỉnh sửa tạo ra các bài test khác nhau (như tăng số lượng người dùng truy cập, thay đổi tham số request, ...).


Một kịch bản có thể đơn giản là:
1. Người dùng truy cập vào trang chủ
2. Người dùng chuyển sang trang admin
3. Người dùng đăng nhập vào trang admin
4. Người dùng đăng xuất khỏi trang admin

### Bước 1: Khởi động `Gatling Recourder` tại máy tính cá nhân

```
cd gatling-charts-highcharts-bundle-3.0.3/bin
./recorder.sh 
```

Kết quả

![](/images/img-cong-cu-gatling-io/galing-1.png)

### Bước 2: Khởi động proxy `Gatling Recorder`
Tại giao diện `Gatling Recorder - Configuration`
- Nhập `localhost HTTP/HTTPS`: `8000` - Khởi tạo proxy sẽ có port bằng 8000
- Nhập `Class Name*`: `RecordedSimulation` - Tên kịch bản sẽ bằng `RecordedSimulation`
- Chọn `start`

Kết quả

![](/images/img-cong-cu-gatling-io/galing-2.png)

### Bước 3: Trỏ Proxy trình duyệt về Proxy Server của Gatling Recorder (sử dụng Chrome)

Trong bài, tôi sẽ sử dụng Google Chrome

- Bước 3.1: Truy cập `chrome://settings/`
- Bước 3.2: Vào mục `System` lựa chọn `Open proxy settings`
- Bước 3.3: Mở popup tùy chỉnh
  ![](/images/img-cong-cu-gatling-io/galing-3.png)



Tại Giao diện cấu hình `Proxy` của Ubuntu, mặc định cấu hình Proxy của Ubuntu sẽ là `Disable`.

- Bước 3.4: Chọn mở Popup `Network Proxy`
  ![](/images/img-cong-cu-gatling-io/galing-4.png)


Tại Popup `Network Proxy`, thiết lập như sau

- Bước 3.5: Lựa chọn `Manual`
- Bước 3.6: Nhập `HTTP Proxy` bằng `localhost` port `8000`
- Bước 3.7: Chọn đóng popup `Network Proxy`
  ![](/images/img-cong-cu-gatling-io/galing-5.png)

Sau khi trỏ thiết proxy Ubuntu về Proxy Server của `Gatling Recorder`, các thao tác trên trình duyệt sẽ được Gatling thu thập từ đó tạo ra các kịch bản test.

### Bước 4: Thực hiện kịch bản

Kịch bản:

1. Người dùng truy cập vào trang chủ
2. Người dùng chuyển sang trang admin
3. Người dùng đăng nhập vào trang admin
4. Người dùng đăng xuất khỏi trang admin

Tại máy chủ web Django, thực hiện kịch bản

Truy cập trang web
```
http://10.10.10.86/
```

Kết quả

![](/images/img-cong-cu-gatling-io/galing-7.png)

Truy cập trang admin
```
http://10.10.10.86/admin/login/?next=/admin/
```

![](/images/img-cong-cu-gatling-io/galing-8.png)

Đăng nhập trang admin

Login vời tài khoản `admin/Cloud365a@123`

![](/images/img-cong-cu-gatling-io/galing-9.png)

Đăng xuất
```
http://10.10.10.86/admin/logout/
```

![](/images/img-cong-cu-gatling-io/galing-17.png)

Kết quả thu được tại Gatling recorder

![](/images/img-cong-cu-gatling-io/galing-6.png)

Sau khi quay xong kịch bản, chọn `stop & save` để gatling sẽ sinh script (`.scale` file)

Kịch bản vừa quay nằm trong đường dần `gatling-charts-highcharts-bundle-3.0.3/user-files/simulations`. Tên kịch bản vừa quay là `RecordedSimulation.scala`

Lưu ý: Xong khi quay xong kịch bản, chuyển proxy trình duyệt về trạng thái `disable` (Nếu không có thể không vào được internet)

## Hướng dẫn sử dụng `Gatling Test`

### Phấn tích file kịch bản
Cấu trúc cơ bản của 1 bài test (`.scala script`) sẽ bao gồm:

- Import các thư viện mặc định
  ```
  val httpProtocol = http
    .baseUrl("http://10.10.10.86")
    .inferHtmlResources()
    .acceptHeader("text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8")
    .acceptEncodingHeader("gzip, deflate")
    .acceptLanguageHeader("en-US,en;q=0.9")
    .upgradeInsecureRequestsHeader("1")
    .userAgentHeader("Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.77 Safari/537.36")
  ```
- Khai báo kịch bản test
  ```
  class RecordedSimulation extends Simulation {
      ...
  }
  ```
- Định nghĩa giao thức (Thông thường là HTTP)
  ```
  val httpProtocol = http
    .baseUrl("http://10.10.10.86")
    .inferHtmlResources()
    .acceptHeader("text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8")
    .acceptEncodingHeader("gzip, deflate")
    .acceptLanguageHeader("en-US,en;q=0.9")
    .upgradeInsecureRequestsHeader("1")
    .userAgentHeader("Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.77 Safari/537.36")
  ```
- Định nghĩa các header bổ sung (Mô phỏng theo thao tác người dùng)
  ```
  val headers_0 = Map(
      "Proxy-Connection" -> "keep-alive",
      "Purpose" -> "prefetch")
  ```
- Định nghĩa kịch bản (Kịch bản thao tác của người dùng)
  ```
  val scn = scenario("RecordedSimulation")
		.exec(http("truy_cap_trang_chu")
			.get("/")
			.headers(headers_0))
		.pause(2)
  ```
- Thực hiện kịch bản
  ```
  setUp(scn.inject(atOnceUsers(1))).protocols(httpProtocol)
  ```

Để vượt cơ chế bảo mật `CRFS` của Django, tôi sẽ bổ sung `token CRFS` vào form khi đăng nhập tài khoản admin.

```c#
val scn = scenario("RecordedSimulation")
		.exec(http("truy_cap_trang_chu")
			.get("/")
			.headers(headers_0))
		.pause(2)
		.exec(http("chuyen_sang_trang_admin")
			.get("/admin/")
            .check(css("input[name=csrfmiddlewaretoken]", "value").saveAs("stoken"))
			.headers(headers_0))
		.pause(7)
		.exec(http("dang_nhap_admin")
			.post("/admin/login/?next=/admin/")
			.headers(headers_2)
			.formParam("csrfmiddlewaretoken", "${stoken}")
			.formParam("username", "admin")
			.formParam("password", "Cloud365a@123")
			.formParam("next", "/admin/"))
        .pause(7)
        .exec(http("dang_xuat_trang_admin")
			.get("/admin/logout/")
			.headers(headers_0))
```

Script đầy đủ [tại đây](https://gist.github.com/lacoski/844d8a05a758d57cdc6710c755e37ccf)

### Chạy kịch bản vừa quay

```
cd gatling-charts-highcharts-bundle-3.0.3/bin
./gatling.sh 
```

Lựa chọn [5] RecordedSimulation
```
GATLING_HOME is set to /home/thanhnb/code/gatling-charts-highcharts-bundle-3.0.3-bundle/gatling-charts-highcharts-bundle-3.0.3
Choose a simulation number:
     [0] BenchCeleryBasicTask
     [1] BenchDatabase
     [2] BenchPortalLB
     [3] LoginDjango
     [4] LoginDjangoCustom
     [5] RecordedSimulation
     [6] computerdatabase.BasicSimulation
     [7] computerdatabase.advanced.AdvancedSimulationStep01
     [8] computerdatabase.advanced.AdvancedSimulationStep02
     [9] computerdatabase.advanced.AdvancedSimulationStep03
     [10] computerdatabase.advanced.AdvancedSimulationStep04
     [11] computerdatabase.advanced.AdvancedSimulationStep05
5
Select run description (optional)
<enter-tại-đây>
Simulation RecordedSimulation started...

================================================================================
2019-02-14 15:58:19                                           5s elapsed
---- Requests ------------------------------------------------------------------
...
Reports generated in 0s.
Please open the following file: /home/thanhnb/code/gatling-charts-highcharts-bundle-3.0.3-bundle/gatling-charts-highcharts-bundle-3.0.3/results/recordedsimulation-20190214085814879/index.html
```

Truy cập đường dẫn kết quả bằng, mở file `index.html` bằng trình duyệt chrome
Kết quả

![](/images/img-cong-cu-gatling-io/galing-10.png)

### Phân tích report

__Tổng quan__

![](/images/img-cong-cu-gatling-io/galing-10.png)

Biểu đồ thể hiện số request đã thực hiện, và phân phối thời gian nhận lại phản hồi trên các request. Theo biểu đồ trên, thời gian thực hiện mỗi request đều nhỏ hơn `800ms`

__Phân tích__

![](/images/img-cong-cu-gatling-io/galing-12.png)

Biểu đồ phân tích thời gian thực hiện các request đã xảy ra. Ở đây, các Request sẽ các file `CSS`, `JS`, các thao tác `GET`, `POST` đã thực hiện trong quá trình mô phỏng. Biểu đồ phân tích thời gian phản hồi của Request (Sớm nhất, chậm nhất, trung bình ...)

__Phân phối người dùng giải lập theo thời gian__

![](/images/img-cong-cu-gatling-io/galing-13.png)

Biểu đồ thể hiện số user giải lập phân phối theo khoảng thời gian thực hiện bài test. Trong bài, tôi sử dụng 1 user giải lập nên sẽ chỉ có 1 user trong suôt khoảng thời gian thực hiện test. Để tăng số user thực hiện giải lập chỉnh lên 10 hoặc lớn hơn, chỉnh giá trị `atOnceUsers` trong `setUp(scn.inject(atOnceUsers(1))).protocols(httpProtocol)` tại Gatling Script.

```
# Thực hiện giả lập 1 user
setUp(scn.inject(atOnceUsers(1))).protocols(httpProtocol)

# Thực hiện giả lập 10 user đồng thời
setUp(scn.inject(atOnceUsers(10))).protocols(httpProtocol)
```

__Phân phối thời gian phản hồi__

![](/images/img-cong-cu-gatling-io/galing-14.png)

Thể hiện phân phối thời gian phản hồi. VD: Có 11.76% (trong 17 request) có thời gian phản hồi trong khoảng 10 ms

__Số request thực hiện theo thời gian__

![](/images/img-cong-cu-gatling-io/galing-15.png)

Thể hiện số request thực hiện trong từng khoảng thời gian thực hiện bài test. VD: trong khoảng thời gian `15:58:18` có 1 user thực hiện 6 request

__Số phản hồi nhận được theo thời gian__

![](/images/img-cong-cu-gatling-io/galing-16.png)

Thể hiện số phản hồi nhận được trong từng khoảng thời gian thực hiện bài test. VD: trong khoảng thời gian `15:58:18` có 1 user nhận được 6 phản hồi (ứng với phản hối request theo thời gian)

## Tổng kết
`Gatling.io` là công cụ rất mạnh mẽ khi muốn đánh giá hiệu năng của Web Server . Ngoài giao thức HTTP, Gatling còn hỗ trợ nhiều giao thức khác như `WebSockets`, `JMS`. Đồng thời, Gatling cho phép tích hợp với một số IDE tăng tính tiện lợi khi thực hiện test (VD: Eclipse), tích hợp với Jenkins.

Để tìm hiểu thêm, các bạn có thể truy cập [Gatling Blog](https://gatling.io/blog/) hoặc [Gatling Blog](https://gatling.io/docs/current/).

# Nguồn

https://gatling.io/docs/current/general/

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>