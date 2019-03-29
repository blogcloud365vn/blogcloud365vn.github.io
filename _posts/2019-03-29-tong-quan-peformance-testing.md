---
date: 2019-03-29
title: "Performance Testing Seri -Tổng quan về khái niệm Performance Testing"
categories:
  - Linux
description: Tổng quan về khái niệm Performance Testing
author: thanhnb
tags: [Gatling, Linux, CentOS]
type: Document
---

## Tổng quan

Kiểm thử hiệu năng (Performance Testing) là kỹ thuật kiểm thử nhằm xác định băng thông, khả năng xử lý, khả năng mở rộng hay nói chung là hiệu năng của hệ thống dưới khối lượng truy cập, khối được công việc xác định. Kết quả của kiểm thử hiệu năng phục vụ việc điều tra, đo lường, đánh giá hiệu năng thực của hệ thống.

Lưu ý, kiểm thử hiệu năng khác với kiểm thử lập trình. Mục tiêu của kiểm thử lập trình là tìm ra lỗi trong mã nguồn sản phẩm. Còn đối với kiểm thử hiệu năng, mục tiêu của kiểm thử hiệu năng là nhằm tìm ra hiệu năng thực của hệ thông dưới các khối lượng truy cập khác nhau.

![](/images/img-performance-testing/pic2.png)

## Mục tiêu của Performance Testing

Sau đây là các mục tiêu chính của kiểm thử hiệu năng

- Đánh giá hiệu năng sản phẩm: Dự đoán hoặc ước tính hiệu năng thực của sản phẩm
- Đánh giá hạ tầng triển khai: Xác định được ngưỡng của hệ thống đối với hạ tầng triển khai hiện tại, xác định được các tài nguyên cần thiết để mở rộng hệ thống trong tương lai
- Tìm ra các vấn đề ảnh hưởng tới hệ thống và điều chỉnh hệ thống: Từ các kết quả có được qua các bài test và quá trình giám sát sản phẩm trong quá trình test, tìm ra được vấn đề ảnh hưởng tới hiệu năng (Các nút cổ chai) từ đó sửa đổi, cải thiện hiệu năng sản phẩm

Các tiêu chí đánh giá hiệu năng bao gồm

- Thời gian phản hồi (Response time): Xác định ứng dụng phản hồi nhanh hay chậm
- Khả năng mở rộng (Scalability): Xác định với lượng truy cập tăng vọt, khả năng đáp ứng cũng như khả năng mở rộng của hệ thống
- Băng thông thông lượng (Throughput): Số kết nối tối đa mà hệ thống có thể đáp ứng, hay đơn giản và số người truy cập cùng thời điểm tối đa
- Tính ổn định (stability): Sự ổn định của hệ thống dưới các mức tải khác nhau

## Các loại kiểm thử hiệu năng thông dụng

![](/images/img-performance-testing/pic1.jpg)

### Load test

Mô phỏng độ chịu tải của hệ thống. Thường là khi hệ thống hoạt động bình thường và khi có tải cao. Nhắm đánh giá sự ổn định khi sản phẩm chạy trong môi trường thực tế. Mục tiêu đánh giá khả năng đáp ứng hệ thống khi hoạt động bình thường và khi hoạt động tại giờ cao điểm.

Ví dụ: Lập trình viên thiết kế ứng dụng có khả năng chịu tải 1000 người sử dụng đồng thời. Để kiểm chứng năng lực, họ sẽ tạo kịch bản load test với 1000 user giả lập, duy trì bài test trong 1-2 giờ để đánh giá khả năng hoạt động của ứng dụng.

### Stress Test

Mô phỏng hệ thống khi quá tải, xác định khả năng hoạt động, phục hồi, ngưỡng chịu đựng trước khi xảy ra sự cố. Mục tiêu chính của Stress Test là tìm ra ngưỡng đỉnh của hệ thống, tại đó nếu vượt ngưỡng đỉnh hệ thống sẽ xảy ra sự cố và không thể đáp ứng dịch vụ.

Ví dụ: Sau khi ước lượng được khả năng đáp ứng của ứng dụng là 1000 người truy cập đồng thời, lập trình viên quyết định tạo bài test với 1100 người dùng giả lập để kiến ứng dụng xảy ra sự cố để đánh giá khả năng phục hồi của ứng dụng.

### Volume test

Mô phỏng khả năng xử lý hệ thống đối với một lượng dữ liệu lớn. Mục tiêu bài test là đánh giá các vấn đề làm ảnh hưởng tới hiệu năng hệ thống khi phải xử lý lượng dữ liệu rất lớn

Ví dụ: Các trang thương mại điện tử cần lưu trữ rất nhiều thông tin. Để đánh giá được hiệu năng của sản phẩm, lập trình viên quyết định tạo ra db với hàng triệu bản ghi, sau đó thực hiện query, cập nhật dữ liệu trên hệ thống để mổ phỏng các hoạt động thực tế của trang thương mại điện tử. Từ đó đánh giá được hiệu năng thực của sản phẩm.

### Endurance test

Mô phỏng hệ thống hoạt động trong thời gian dài quá đó tìm ra các vấn đề về bộ nhớ, phân mảnh dữ liệu v.v.

Ví dụ: Các trang thương mại điện tử thường phải hoạt động trong thời gian rất dài với lượng truy cập cao, lập trình viên tạo bài test có thời gian tới 2-3 ngày với lượng người dùng truy cập không đổi để đánh giá sự ổn định sản phẩm.

### Spike Test

Kiểm tra phản ứng của hệ thống khi tải thay đổi đột ngột. Đồng thời cũng kiếm tra khả năng phục hồi của hệ thống khi lượng truy cập giảm

Ví dụ: Các trang đăng ký học thường có lượng truy cập tăng vọt tại thời điểm đăng ký môn học và chỉ duy trì trong thời gian 1-2 tiếng. Lập trình viên tạo bài test với tải tăng đột ngột trong 1-2 giờ sau đó đánh giá khả năng đáp ứng và khả năng phục hồi sau khi tải giảm.


## Tổng kết

Kiểm thư hiệu năng hay Performance là kỹ thuật kiểm thử nhằm xác định sự ổn định của hệ thống hay ứng dụng. Qua đó cho người quản trị, người lập trình viên có cái nhin tổng quan về hiệu năng, ngưỡng chịu đựng, khả năng đáp ứng của sản phẩm qua đó có thể tối ưu, thiết kế các giải pháp backup phù hợp.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>