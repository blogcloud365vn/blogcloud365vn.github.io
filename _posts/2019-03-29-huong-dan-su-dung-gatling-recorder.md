---
date: 2019-03-29
title: "Performance Testing - Phần 2 - Hướng dẫn sử dụng Gatling Recoder"
categories:
  - Linux
description: Performance Testing - Phần 2 - Hướng dẫn sử dụng Gatling Recoder
author: thanhnb
tags: [Gatling, Linux, CentOS]
type: Document
---

## Tổng quan

Gatling Recorder hỗ trợ người dùng sinh ra kịch bản test nhanh chóng bằng cách thu thập các request HTTP diễn ra giữa trình duyệt và trang web muốn kiếm thử mong muốn.

## Chuẩn bị

### Mô hình

![](/images/img-su-dung-gatling-recorder/galing-mohinh.png)


Sau đây mình sẽ sử dụng công cụ Gatling Recoder để  quay lại thao tác giữa người dùng và một trang wordpress đơn giản

## Cách sử dụng Gatling Recoder

Mình sẽ tạo kịch bản với mục tiêu giải lập thao tác người dùng truy cập vào trang wordpress do mình triển khai.

Kịch bản một user truy cập sẽ là

- Bước 1: Người dùng truy cập trang chủ
- Bước 2: Chuyển sang đọc một bài viết
- Bước 3: Tìm kiếm một bài viết
- Bước 4: Truy cập một bài viết trong danh sách có được từ bước 3
- Bước 5: Người dùng trở về trang chủ

## Bước 1: Khởi động Gatling Recorder

Truy cập thư mục cài đặt gatling, khởi động gatling recorder

```
cd gatling-charts-highcharts-bundle-3.0.3/bin
./recorder.sh 
```

![](/images/img-su-dung-gatling-recorder/pic1.png)

Lưu ý:

- Bước 1.1: Lựa chọn Port sử dụng cho proxy
- Bước 1.2: Nhập tên Class hay tên kịch bản sẽ được tạo mới
- Bước 1.3: Khi chỉ định xong 2 thông tin trên, chọn `start`

Kết quả

![](/images/img-su-dung-gatling-recorder/pic2.png)

## Bước 2: Trỏ Proxy về Proxy server của Gatling Recorder

Trong bài mình sẽ sử dụng Google Chrome

![](/images/img-su-dung-gatling-recorder/pic3.png)

- Bước 2.1: Truy cập đường dẫn `chrome://settings/`
- Bước 2.2: Tìm kiếm từ khóa proxy
- Bước 2.2: Truy cập system - chọn Open proxy settings
- Bước 2.3: Mở settings Network Ubuntu

Kết quả

![](/images/img-su-dung-gatling-recorder/pic4.png)

- Bước 2.4: Tại giao diện quản trị Network, chọn settings

![](/images/img-su-dung-gatling-recorder/pic5.png)

- Bước 2.5: Lựa chọn Manual
- Bước 2.6: Nhập HTTP Proxy bằng localhost port 8000
- Bước 2.7: Chọn đóng popup Network Proxy

Sau khi trỏ thiết proxy Ubuntu về Proxy Server của Gatling Recorder, các thao tác trên trình duyệt sẽ được Gatling thu thập từ đó tạo ra các kịch bản test.

Lưu ý:
- Sau khi quay xong kịch bản test, bạn cần phải disable proxy tại Ubuntu để có thể truy cập Internet bình thường

![](/images/img-su-dung-gatling-recorder/pic6.png)

## Bước 3: Quay kịch bản

Sau đây, mình sẽ thực hiện các thao tác sau trên trình duyệt, để tạo ra kịch bản ban đầu.

### Bước 3.1: Người dùng truy cập trang chủ

![](/images/img-su-dung-gatling-recorder/pic7.png)



### Bước 3.2: Chuyển sang đọc một bài viết

![](/images/img-su-dung-gatling-recorder/pic8.png)

Kết quả

![](/images/img-su-dung-gatling-recorder/pic9.png)

### Bước 3.3: Tìm kiếm một bài viết

![](/images/img-su-dung-gatling-recorder/pic10.png)

Kết quả

![](/images/img-su-dung-gatling-recorder/pic11.png)

### Bước 3.4: Truy cập một bài viết trong danh sách có được từ bước 3

![](/images/img-su-dung-gatling-recorder/pic12.png)

Kết quả

![](/images/img-su-dung-gatling-recorder/pic13.png)

### Bước 3.5: Người dùng trở về trang chủ

![](/images/img-su-dung-gatling-recorder/pic14.png)

Kết quả

![](/images/img-su-dung-gatling-recorder/pic15.png)

## Bước 4: Lưu kịch bản

![](/images/img-su-dung-gatling-recorder/pic16.png)

![](/images/img-su-dung-gatling-recorder/pic17.png)

Kịch bản sau khi quay xong sẽ nằm trong được dẫn `Simulations folder`

## Kiểm tra

Kiểm tra đường dẫn `Simulations folder` và chúng ta được kết quả như sau

![](/images/img-su-dung-gatling-recorder/pic18.png)

Script tham khảo [tại đây](https://gist.github.com/lacoski/624e1be15c969e1bbfd15b2b7239cb6d)

## Tổng kết

Đến đây, mình đã hướng dẫn các bạn sử dụng Gatling Recorder, đến bài tiếp theo mình sẽ hướng dẫn các bạn sử dụng Gatling để chạy kịch bản vừa quay, thay đổi cách tạo tải. Cám ơn các bạn đã quan tâm

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>