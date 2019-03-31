---
date: 2019-04-01
title: "Performance Testing - Phần 5 - Hướng dẫn ước lượng tải của Web bằng Gatling"
categories:
  - Linux
description: Hướng dẫn ước lượng tải của Web bằng Gatling
author: thanhnb
tags: [Gatling, Linux, CentOS]
type: Document
---

## Tổng quan

Gatling là công cụ phục vụ cho công việc kiểm thử hiệu năng của hệ thống. Vậy làm sao để có thể đánh giá hiệu năng của Web bằng Gatling? Sau đây mình sẽ hướng dẫn các bạn sử dụng Gatling, tìm ra ngưỡng tải của một trang web.

## Chuẩn bị

Mình sẽ sử dụng Gatling để kiểm thử hiệu năng một trang Web Wordpress đơn giản (Trang Wordpress sử dụng template tiểu chuẩn do Wordpress cung cấp). Trang Web Wordpress của mình được deploy trên Cloud VPS có cấu hình 3 core, 2 GB Ram, 2 GB Swap, 50 GB ổ đĩa, chạy hệ điều hành CentOS 7. Ngoài ra, mình sẽ sử dụng lại kịch bản có được từ phần 2 - "Hướng dẫn sử dụng Gatling Recorder" và các kiến thức có được tài phần 4 - "Hướng dẫn sử dụng Gatling Report"

Đồng thời mình có cái đặt thêm script giám sát nhỏ. Các bạn tham khảo tại [đây](https://github.com/lacoski/monitor-web)

## Bắt đầu

Để đánh giá được ngưỡng của trang Web, mình sử dụng dụng 2 loại test là Load test, Spike test

### Load test

Nhắc lại, Load test là bài kiểm thử phỏng độ chịu tải của hệ thống. Thường là khi hệ thống hoạt động bình thường và khi có tải cao. Nhắm đánh giá sự ổn định khi sản phẩm chạy trong môi trường thực tế. Mục tiêu đánh giá khả năng đáp ứng hệ thống khi hoạt động bình thường và khi hoạt động tại giờ cao điểm.

Để thực hiện bài load test đơn giản, mình sẽ lần lượt thử inject user theo kịch bản "có n user truy cập đồng thời" và tăng dần n từ 20 tới 60 user đồng thời.

Tại lần thử thực hiện 60 user truy cập đồng thời, mình nhận thấy trang web đã tới ngưỡng. Đế đánh giá được trang web đã tới ngưỡng mình theo dõi file log và kết quả report có được

Phân tích log

![](/images/img-danh-gia-nguong-gatling/pic1.png)

Tại thời điểm đỉnh, tài nguyên CPU đã sử dụng tới 100%, Ram sử dụng 1.6G trên tổng 1.8G, Swap sử dụng 2.0G trên 2.0G. Load trung bình của Web trong 1 phút là 20. Tổng số kết nối tới là 824.

Phân tích report

![](/images/img-danh-gia-nguong-gatling/pic2.png)

Đánh giá bài test 60 user truy cập đồng thời:
- Tổng thực hiện 2640 request
- Tất cả request đều thành công
- Trung bình thực hiện 18.592 request trên 1 giây
- Phản hồi nhanh nhất 3 ms
- Phản hồi chậm nhất 21373 ms tức hơn 21s (Phản hồi rất chậm)

![](/images/img-danh-gia-nguong-gatling/pic3.png)

Tại thời điểm 23:19:57, biểu đồ phân phối phản hồi theo thời gian:
- Phản hồi chậm nhất là 21373 ms tức hơn 21s
- Có 60 user truy cập cùng thời điểm

Nhận xét:
- Nếu đối sánh với file log có được, thời điểm 23 giờ 19 phút 57 giây tài nguyên của Cloud VPS đã sử dụng hết, nên xảy ra hiện tượng tác nghẽn hoặc bị treo tạm thời vì vậy phản hồi lại sẽ rất chậm. Vậy trang web sẽ hoạt động bình thường với khoảng 50 - 60 user truy cập cùng thời điểm.

### Spike test
Nhắc lại, Spike test là bài kiểm thử phản ứng của hệ thống khi tải thay đổi đột ngột. Đồng thời cũng kiếm tra khả năng phục hồi của hệ thống khi lượng truy cập giảm. 

Tại bài load test, chúng ta đã xác định được trang web sẽ hoạt động ổn định khi có lượng truy cập đồng thời giao động từ 50 - 60 user và với khoảng 800 - 1000 kết nối. Dựa trên thông tin trên, mình sẽ thực hiện bài spike test với kịch bản "có 200 user truy cập trong vòng 300 giây". Bài test sẽ đẩy ngưỡng kết nối lên tới 1583 và thời điểm đỉnh sẽ có 88 user truy cập đồng thời.


Phân tích report

![](/images/img-danh-gia-nguong-gatling/pic6.png)

Mình sẽ tập trung phân tích biểu đồ phân phối phản hồi theo thời gian.

![](/images/img-danh-gia-nguong-gatling/pic7.png)


Tài khoảng thời gian Web có lượng truy cập từ 1 tới 62 user truy cập, trang Web hoạt động bình thường.

![](/images/img-danh-gia-nguong-gatling/pic8.png)

Nhưng khi số lượng truy cập vướt quá 63, phản hồi của Web sẽ tăng cao do tài nguyên có thể sử dụng của trang Web đã hết, các request mới sẽ được đưa vào hàng chờ. Và nếu trong thời điểm đó, lượng request mới vẫn tiếp tục đổ vào hoặc vẫn có user tiếp tục truy cập trang Web thì phản hồi sẽ ngày càng tăng cho đến thời điểm Web sẽ treo tạm thời (nghẽn hàng chờ). Đỉnh điểm là thời điểm có 83 user truy cập, phản hồi lên tới trên 60 giây, và bắt đầu xuất hiện hiện tượng `connection refused` (Tức không thể kết nối thêm tới trang web). Mình gọi là đỉnh điểm vì tới thời điểm 83 user truy cập cùng lúc, lượng `CUNG` đã hết (tức đã tới user thứ 200 truy cập) nên lượng truy cập sẽ dần giảm xuống thời thời gian.

Phân tính log

![](/images/img-danh-gia-nguong-gatling/pic9.png)

Đỉnh điểm là thời điểm lượng kết nối mở ra tới 1583, cung là lúc năng lực của hệ thống đã tới giới hạn. Sau thời điểm này load trung bình trong 1 phút sẽ không nhưng tăng cao (từ 11 cho tới đỉnh là 106). Như mình đã nói từ trước, trang Web sẽ chỉ hoạt động bình thường khi load trung bình dưới 20, tại thời điểm load trung bình trên 40 phải hồi của trang web sẽ giao đồng từ 40s hoặc treo tạm thời.

![](/images/img-danh-gia-nguong-gatling/pic10.png)

Mục tiêu thứ 2 của spike test là kiếm tra khả năng phục hồi, dựa theo log có được thời điểm 23:13:31 là thời điểm bắt đầu treo tạm thời (load trung bình 60) và tới đỉnh là 23:16:01 (load trung bình 112) và phải tới thời điểm 23:19:39 web mới bắt đầu ổn định trở lại (load trung bình 19.92). Vậy mình ước tính sẽ mất 4 phút để web có thể phục hồi khi xảy ra hiện tượng treo tạm thời.

## Tổng kết

Qua 2 bài kiểm thử load test và spike test mình đã có những thông tin cần thiết để ước lượng được ngưỡng truy cập ổn định trang web và thời gian để trang web phục hồi sau khi quá tải.

Tới đây mình đã hướng dẫn các bạn ước lượng tải của Web bằng công cụ Gatling. Cám ơn các bạn đã quan tâm

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>