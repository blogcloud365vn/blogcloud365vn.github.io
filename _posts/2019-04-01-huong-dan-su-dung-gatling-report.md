---
date: 2019-03-29
title: "Performance Testing - Phần 4 - Hướng dẫn sử dụng Gatling Report"
categories:
  - Linux
description: Tổng quan về khái niệm Performance Testing
author: thanhnb
tags: [Gatling, Linux, CentOS]
type: Document
---

## Tổng quan

Một trong nhưng ưu điểm của Gatling khi đánh giá với các công cụ benchmark web khác là khả năng xuất report đơn giản, dễ hiểu. Trong bài mình sẽ hướng dẫn các bạn đọc, phân tích biểu đồ của Gatling Report.

## Chuẩn bị

Trước khi bắt đầu, chúng ta cần report mẫu để phân tích. Ở đây mình sử dụng bài test có được từ phần 2 và chạy 1 kịch bản test để lấy kết quả.

Mình sẽ thực hiện bài test tạo ra 150 user trong vòng 300 giây tức cứ mỗi 2 giây Gatling sẽ tạo ra 1 user thực hiện thao tác giả lập.

Đồng thời mình cũng cài đặt script monitor nhỏ lên web mình benchmark để log hỗ trợ việc phân tích. Các bạn tham khảo script tại [đây](https://github.com/lacoski/monitor-web)

## Phân tích report

### Biểu đồ 1: Biểu đồ tổng quan

![](/images/img-huong-dan-su-dung-report/pic1.png)

Biểu đồ 1 sẽ gồm biểu đồ `Indicatos` và `Number of request`. Mục tiêu miêu tả tổng quan về phân phối phản hồi trong các phạm vi tiểu chuẩn cũng như các request thất bại. VD, bài test đã thực hiện hơn 6000 request

Mặc định Gatling sẽ đánh giá phản hồi như sau:

- Từ 0 - 800 ms: phản hồi tốt (Phản hồi nhanh)
- Từ 800 ms - 1200 ms (1.2 giây): phản hồi trung bình
- Trên 1200 ms (1.2 giây): là phản hồi chậm

Chúng ta có thể chỉnh sửa lại các ngưỡng trên bằng tùy chỉnh lại settings gatling tùy nhiên mình sẽ hướng dẫn các bạn trong bài khác.

### Biểu đồ 2: Phân tích phản hồi của request

![](/images/img-huong-dan-su-dung-report/pic2.png)

Biểu đồ 2 phân tích phản hồi của các request đã thực hiện. Bao gồm số request thành công, thất bại, tỷ lệ gửi request mỗi giây, phản hồi nhanh nhất và chậm nhất, trung bình trong khoảng ... Ví dụ, loại request truy cập trang chủ có thời gian phản hồi nhanh nhất là 3 ms và chậm nhất là 4696 ms tức khoảng 4,6 giây.

Ngoài ra chúng ta có thể xem chi tiết phân tích 1 loại request, mục tiêu đánh giá 1 tính năng xử lý nhanh hay chậm

Ví dụ

![](/images/img-huong-dan-su-dung-report/pic3.png)

Phân tích:

- Request có tên "xem_bai_viet_5" 
- Thực hiện tổng cộng 300 request và không có thất bại
- Thời gian phản hồi nhanh nhất 357 ms
- Thời gian phản hồi chậm nhất 6921 ms tức 6.9 giây
- Phản hồi trung bình là 748 ms

### Biểu đồ 3: Phân phối user được gatling tạo theo thời gian

![](/images/img-huong-dan-su-dung-report/pic4.png)

Biểu đồ 3 thông kế số user đang thực hiện kịch bản test hay có bao user mô phòng đang truy cập web trong khoảng thời gian bài test được chạy. Ví dụ tại thời điểm A có 10 user, tại thời điểm B có 15 user.

Ví dụ

![](/images/img-huong-dan-su-dung-report/pic5.png)

Phân tích:
- Tại thời điểm 23 giờ 25 phút 33 giây có 23 user ảo đang thực hiện bài test
- Các user ảo được tạo ra bởi kịch bản `Nguoi_dung_vang_lai_1`

### Biểu đồ 4: Phân phối phản hồi

![](/images/img-huong-dan-su-dung-report/pic6.png)

Biểu đồ 4 thể hiện phân phối thời gian phản hồi của tất cả request. VD, có tầm 78% request có phản hồi trong vòng 38 ms

Ví dụ

![](/images/img-huong-dan-su-dung-report/pic7.png)

Phân tích:
- Có tới 78.13% trên tổng các request có phản hồi bằng 38 ms

### Biểu đồ 5: Phân phối phản hồi theo thời gian

![](/images/img-huong-dan-su-dung-report/pic8.png)

Biểu đồ 5 thể hiện phân phối thời gian phản hồi của tất cả request theo khoảng thời gian. VD, tại thời điểm A, request có phản hồi nhanh nhất là 8 ms, phản hồi chậm nhất là 399 ms.

Ví dụ

![](/images/img-huong-dan-su-dung-report/pic9.png)

Phân tích
- Tại khoảng thời gian 23 giờ 25 phút 32 giây
- Phản hồi nhanh nhất mất 8 ms
- Phản hồi chậm nhất mất 399 ms
- Có 23 người truy cập tại thời điểm

![](/images/img-huong-dan-su-dung-report/pic10.png)

Phân tích
- Tại khoảng thời gian 23 giờ 28 phút 28 giây
- Phản hồi nhanh nhất mất 10 ms
- Phản hồi chậm nhất mất 6749 ms tức 6.7s
- Có 25 người truy cập tại thời điểm

### Biểu đồ 6: Phân phối request gửi đi trong một giây

![](/images/img-huong-dan-su-dung-report/pic11.png)

Biểu đồ 6 thể hiện tổng số request thực hiện trong một giây theo khoảng thời gian thực hiện bài test. VD, tại thời điểm A thực hiện tổng cộng 22 request.

Ví dụ

![](/images/img-huong-dan-su-dung-report/pic12.png)

Phân tích
- Tại khoảng thời gian 23 giờ 25 phút 19 giây
- Có tổng 22 request được thực hiện
- Có 23 người truy cập tại thời điểm

![](/images/img-huong-dan-su-dung-report/pic13.png)

Phân tích
- Tại khoảng thời gian 23 giờ 28 phút 35 giây
- Có tổng 63 request được thực hiện
- Có 25 người truy cập tại thời điểm

### Biểu đồ 7: Phân phối phản hồi nhận được trong một giây

![](/images/img-huong-dan-su-dung-report/pic14.png)

Biểu đồ 7 thể hiện tổng số phản hồi nhận được trong một giây theo khoảng thời gian thực hiện bài test. VD, tại thời điểm A nhận được tổng cộng 22 phản hồi.

Ví dụ

![](/images/img-huong-dan-su-dung-report/pic15.png)

Phân tích
- Tại khoảng thời gian 23 giờ 25 phút 05 giây
- Có tổng 22 phản hồi nhận được
- Có 23 người truy cập tại thời điểm


![](/images/img-huong-dan-su-dung-report/pic16.png)

Phân tích
- Tại khoảng thời gian 23 giờ 28 phút 35 giây
- Có tổng 65 phản hồi nhận được
- Có 25 người truy cập tại thời điểm

## Tổng kết

Kịch bản mình thực hiện là tạo ra 150 user trong vòng 300 giây tức cứ 2 giây Gatling sẽ tạo ra 1 user thực hiện thao tác giả lập tới trang web chỉ định.

Qua report mình thông tin mình có được:
- Thời điểm có số user truy cập cao nhất là 25
- Tổng số request gửi đi nhiều nhất là 65
- Phản hồi nhanh nhất là 3 ms
- Phản hồi chậm nhất là 6921 ms tức 6.9 giây

![](/images/img-huong-dan-su-dung-report/pic17.png)

Đồng thời, mình một script giám sát đơn giản và thấy được tổng kết nối tới web cao nhất là 950 kết nối, sử dụng 1.4 GB Ram, 300M Swap, load trung bình của CPU là 3.10.

Đến đây mình đã giới thiệu các bạn các đọc và phân tích report, đến bài tiếp theo mình sẽ hướng dẫn các bạn các ước lượng ngưỡng của trang web.

## Nguồn

https://gatling.io/docs/2.2/general/reports/

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>