---
title: "Nextcloud [Part 11] - Cài đặt quản lý các ứng dụng"
categories:
  - Nextcloud
description: Nextcloud - Cài đặt quản lý các ứng dụng
author: canhdx
tags: [Nextcloud]
type: Document
set: NextCloud
set_order: 17
---

## NextCloud Application

NextCloud có nhiều tính năng được tích hợp sẵn khi bạn cài đặt.
<p align="center">
<img src="/images/img-nextcloud/nextcloud-063.png">
</p>

NextCloud chia các tính năng này thành các application khác nhau như các module, chúng kết hợp lại với nhau và tạo nên một hệ sinh thái tổng thể nhằm cung cấp nhiều tiện ích nhất cho người sử dụng. 

Ngoài ra NextCloud còn cung cấp một kho khá phong phú các Application để bạn có thể cài đặt thêm. Bài viết sẽ hướng dẫn các bạn cài đặt bổ sung các Application mới cho NextCloud

## Cài đặt thêm các Application cho NextCloud 
Việc cài đặt thêm các Application cho một hệ thống NextCloud có sẵn chỉ có những tài khoản có quyền admin mới được phép thao tác. Và chỉ được thực hiện trên WEB.

Đăng nhập bằng tài khoản có quyền admin
<p align="center">
<img src="/images/img-nextcloud/nextcloud-064.png">
</p>

Click vào icon của tài khoản chọn `+ Apps`  
<p align="center">
<img src="/images/img-nextcloud/nextcloud-065.png">
</p>

Ở giao diện này chúng ta có thể phân loại các app theo menu phía bên tay trái
<p align="center">
<img src="/images/img-nextcloud/nextcloud-066.png">
</p>

- `Your apps`: Toàn bộ các app được cài đặt 
- `Active apps`: Các app đang được bật 
- `Disable apps`: Các app đã được cài đặt nhưng không bật lên, không sử dụng
- `Update`: Các app có bản cập nhật mới 
- `App bundles`: Phân loại các app theo từng gói

Ngoài ra hệ thống còn phân loại apps theo từng nhóm ứng dụng phục vụ mục đích cụ thể bao gồm
- `Files`: Các apps liên quan đến việc quản lý, lưu trữ, hiển thị file
- `Games`: App hỗ trợ
- `Integrations`: Các apps tích hợp liên quan đến việc xử lý lỗi hệ thống 
- `Monitoring`: Theo dõi và giám sát các tài nguyên (Từ phần cứng đến tài nguyên sử dụng)
- `Multimedia`: Các apps liên quan đến các tài liệu dạng đa phương tiện (Videos, âm thanh, hình ảnh,...)
- `Office & text`: Các apps cung cấp các tiện ích hỗ trợ cho các tài liệu dạng office và text
- `Search`: Các apps liên quan đến tìm kiếm, duyệt tài liệu
- `Social & communication`: Hỗ trợ các tác vụ về liên kết chia sẻ nhanh các tài liệu qua các mạng xã hội
- `Tools`: Các tools hỗ trợ

Việc tìm kiếm các app được hỗ trợ ngay trên thanh tìm kiếm.
<p align="center">
<img src="/images/img-nextcloud/nextcloud-067.png">
</p>

Để cài đặt 1 apps nào đó chúng ta tìm kiếm và chọn `Download and Enable` ở đây mình chọn app `Draw.io`
<p align="center">
<img src="/images/img-nextcloud/nextcloud-068.png">
</p>

Quay lại trang chủ chúng ta thấy `Diagram` được kích hoạt ngay lập tức không cần phải restart lại service hay làm bất cứ một thao tác gì
<p align="center">
<img src="/images/img-nextcloud/nextcloud-069.png">
</p>

## Các applicaion thường sử dụng 

- `Calendar`: Quản lý lịch
- `Tasks`: Quản lý task công việc 
- `Draw.io`: Hỗ trợ vẽ sơ đồ online 
- `Collabora Online`: Office cho phép đọc ghi các file office trên web
- `Collaborative tags`: Hỗ trợ đánh tags tài liệu 
- `File access control`: Quản lý rules đối với tài liệu 
- `Registration`: Cho phép mở tính năng đăng ký tài khoản
- `Extract`: Giải nén các file nén trên web 
- `Mind Map`: Hỗ trợ vẽ sơ đồ tư duy online 
- `User usage report`: Report về sử dụng tài nguyên của user
- Và các apps khác 

## Gỡ các Application đang chạy trên NextCloud
Đối với apps trong Next Cloud có 3 trạng thái. 
- (Gỡ)Chưa cài 
- Cài đặt và kích hoạt 
- Hủy kích hoạt apps
- Gỡ(Chưa cài)

Các thao tác deactive apps cũng tương tự như khi cài đặt app. Truy cập `Apps` chọn mục `Active apps` hoặc search tên apps chọn `Disable`
<p align="center">
<img src="/images/img-nextcloud/nextcloud-070.png">
</p>

Chúng ta có thể `Enable` lại apps hoặc `Remove` xóa luôn apps 
<p align="center">
<img src="/images/img-nextcloud/nextcloud-071.png">
</p>


## Các tính năng phát triển

Hệ thống NextCloud còn cho phép kết nối các hệ thống lưu thứ 3 như S3, GoogleDrive , ... Việc kho apps được mở khiến chúng ta có thể tùy biến các tính năng phục vụ cho mục đích lưu trữ dữ liệu của doanh nghiệp bạn.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
