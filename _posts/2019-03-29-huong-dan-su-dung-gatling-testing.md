---
date: 2019-03-29
title: "Hướng dẫn sử dụng Gatling Test"
categories:
  - Linux
description: Hướng dẫn sử dụng Gatling Test
author: thanhnb
tags: [Gatling, Linux, CentOS]
type: Document
---

## Tổng quan

Sau bài 2, mình đã hướng dẫn các bạn quay một kịch bản gatling script đơn giản. Trong bài viết này, mình sẽ hướng dẫn các bạn sử dụng công cụ gatling để chạy kịch bản cũng như điểu chỉnh cách tạo tải trong gatling. Gatling sử dụng ngôn ngữ scala để tạo ra các kịch bản nên mình khuyến khích các bạn tìm hiểu ngôn ngữ scala cơ bản.

## Phân tích kịch bản

Ở đây mình sẽ mở kịch bản quay từ bài 2. Trong gatling, file chứ gatling script sẽ nằm trong thư mục `gatling-charts-highcharts-bundle-3.0.3/user-files/simulations`

Script tham khảo [tại đây](https://gist.github.com/lacoski/624e1be15c969e1bbfd15b2b7239cb6d)


Trong bài viết này, mình sẽ sử dụng công cụ Visual Studio Code để đọc cũng như chỉnh sửa script gatling. Tải công cụ Visual Studio Code tại [link](https://code.visualstudio.com/)

### Phân tích cấu trúc

File kịch bản mình có được sau bài 2 có tên `RecordedSimulation.scala`

Cấu trúc tổng quát

![](/images/img-huong-dan-su-dung-gatling-test/pic1.png)

Theo cấu trúc mình sẽ chia thành 6 phần
- (1): Khai báo các thư viện của Gatling
- (2): Tên của kịch bản
- (3): Khai báo giao thức, trong bài mình sử dụng giao thức http
- (4): Danh sách các header request
- (5): Kịch bản mô phỏng người dùng
- (6): Tải mô phỏng


Trong file kịch bản chúng ta tạm không cần quan tâm tới các thành phần 1, 2, 3, 4. Mình sẽ tập trung phân tích phần 5 và phần 6, đây là 2 phần quan trọng để chỉnh sửa cũng như tạo tải mô phỏng

### Phân tích kịch bản mô phỏng

![](/images/img-huong-dan-su-dung-gatling-test/pic2.png)


Tại kịch bản `scn`, mình đã chia thành 5 step lớn tương ứng với kịch bản mình quay trong bài 2.

Tóm tắt các thao tác mình đã thực hiện:

- Bước 1: Người dùng truy cập trang chủ
- Bước 2: Chuyển sang đọc một bài viết
- Bước 3: Tìm kiếm một bài viết
- Bước 4: Truy cập một bài viết trong danh sách có được từ bước 3
- Bước 5: Người dùng trở về trang chủ


Các step mình đóng khung là cách thao tác mình thực hiện trên trình duyệt được Gatling Recorder thu thập lại. Thông thường mỗi `exec` sẽ tương ứng với 1 thao tác trên trình duyệt

Bây giờ chúng ta sẽ tập trung phân tích 1 step hay 1 thao tác. Ở đây mình sẽ phân tích bước 1, người dùng truy cập trang chủ.

![](/images/img-huong-dan-su-dung-gatling-test/pic3.png)

Tại bước 1, người dùng thực hiện thao tác truy cập trang chủ.

Mã nguồn

```
.exec(
    http("request_0")
    .get("/")
    .headers(headers_0)
    .resources(
        http("request_1")
        .get("/wp-content/themes/karuna-wpcom/assets/fonts/genericons/genericons/genericons.css")
        .headers(headers_1),
        http("request_2")
        .get("/wp-includes/js/wp-emoji-release.min.js?ver=4.9.10")
        .headers(headers_2),
        http("request_3")
        .get("/favicon.ico")
        .headers(headers_3)
        .check(status.is(404))
    ))
```

Phân tích:

- block `exec` sẽ bằng với một thao tác trên trình duyệt (click vào 1 link hay button)
- `http("request_0")` thực hiện giao thức http, với tên requét là `request_0`. Chúng ta hoàn toàn có thể đổi tên `request_0` thành `truy_cap_web` để script trở nên dễ hiểu hơn
- `headers(headers_0)` thực hiện request với `headers_0`. Như mình đã nói, gatling sẽ cố gắng mô phỏng thao tác người dùng. Thì để request mô phỏng giống request thực, gatling sẽ bổ sung thêm các `param` và header request.
- block `resources` sẽ mô phỏng các request ẩn phía dưới trang web. VD: Khi vào trang web, trình duyệt sẽ tự động load thêm các file css, js phía dưới. Vậy ở đây, gatling đã mô phỏng thêm 3 request chạy ẩn phía dưới trình duyệt (`request_1`, `request_2`, `request_3`)



### Phân tích tải mô phỏng

Để tạo ra tải mô phỏng, Gatling sử dụng block `setUp`. Bên trong block `setUp` là cách kịch bản sẽ được thực hiện và cách mỗi kịch bản được mô phỏng, VD: Thực hiện kịch bản A và B, kịch bản A thực hiện với 10 người dùng đồng thời, kịch bản B với 5 người dùng mỗi 1 giây duy trì trong 5 giây.

Mã nguồn

```
setUp(
    scn.inject(
        atOnceUsers(1)
    )
).protocols(httpProtocol)
```

- Block `setUp`, chứa các kịch bản sẽ được thực hiện
- `scn` tên của kịch bản trong `val scn = scenario("RecordedSimulation")`
- `scn.inject` các kịch bản tạo tải, ví dụ 10 người dùng đồng thời thực hiện kịch bản, mỗi 1 giây tạo ra một người dùng
- `atOnceUsers(1)` kịch bản tạo tải, thực hiện mô phỏng 1 người dùng thực hiện các thao tác mô phỏng.

### Chạy kịch bản

Khởi động Gatling
```
cd gatling-charts-highcharts-bundle-3.0.3/bin
./gatling.sh
```

Chọn kịch bản vừa quay

```
GATLING_HOME is set to /home/thanhnb/Bench/gatling-charts-highcharts-bundle-3.0.3
Choose a simulation number:
     [0] LandingBench
     [1] RecordedSimulation
     [2] computerdatabase.BasicSimulation
     [3] computerdatabase.advanced.AdvancedSimulationStep01
     [4] computerdatabase.advanced.AdvancedSimulationStep02
     [5] computerdatabase.advanced.AdvancedSimulationStep03
     [6] computerdatabase.advanced.AdvancedSimulationStep04
     [7] computerdatabase.advanced.AdvancedSimulationStep05
1 < CHỌN 1 ĐÚNG KỊCH BẢN VỪA QUAY>
Select run description (optional)
<ENTER TẠI ĐÂY>
Simulation RecordedSimulation started...
```

Kết quả
```
Reports generated in 0s.
Please open the following file: /home/thanhnb/Bench/gatling-charts-highcharts-bundle-3.0.3/results/recordedsimulation-20190324145718601/index.html
```

## Thay đổi tải mô phỏng

Để thay đổi tải mô phỏng, chúng ta sẽ sửa lại block `setUp` trong script.

### Mẫu 1: Thực hiện 1 user đồng thời

```
setUp(
    scn.inject(
        atOnceUsers(1)
    )
).protocols(httpProtocol)
```

### Mẫu 2: Thực hiện 10 user đồng thời

```
setUp(
    scn.inject(
        atOnceUsers(10)
    )
).protocols(httpProtocol)
```

### Mẫu 3: Cứ 5 giây tạo ra một user giả lập, duy trì trong 500 giây

```
setUp(
    scn.inject(
        rampUsers(100) during(500 seconds)
    )
).protocols(httpProtocol)
```

### Mẫu 4: Cứ 1 giây tạo ra 3 user giả lập, duy trì trong 15 giây

```
setUp(
    scn.inject(
        constantUsersPerSec(3) during (15 seconds)
    )
).protocols(httpProtocol)
```

### Mẫu 5: Kết hợp 2 kịch bản tạo tải

Kết hợp kịch bản 10 user đồng thời và cứ 5 giây tạo ra một user giả lập, duy trì trong 500 giây

```
setUp(
    scn.inject(
        atOnceUsers(10),
        rampUsers(100) during(500 seconds)
    )
).protocols(httpProtocol)
```

Lưu ý:

- Có thể kết hợp nhiều kịch bản mô phỏng tải
- Có thể thêm các kiểu dữ liệu, cú pháp ngôn ngữ scala để tạo nhiều kịch bản phức tạp hơn
- Để xem thêm cách tạo tải, tham khảo thêm gatling cheetsheet

## Tổng kết

Đến đây, mình đã hướng dẫn các bạn sử dụng Gatling Test và phân tích cấu trúc script scala, thay đổi cách tạo tải. Đến bài tiếp theo mình sẽ hướng dẫn các bạn cách phân tích biểu đồ Cám ơn các bạn đã quan tâm

## Nguồn

https://gatling.io/docs/current/cheat-sheet/

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>