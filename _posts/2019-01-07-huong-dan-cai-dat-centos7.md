---
date: 2019-01-07
title: Hướng dẫn cài đặt CentOS 7.6 - 64 bit
categories:
  - Linux
description: Tài liệu hướng dẫn các bước cài đặt CentOS-7-x86_64-Minimal-1810
author: duydm
tags: [Beginer, Linux, CentOS7]
type: Document
---

Tài liệu hướng dẫn các bước cài đặt CentOS-7-x86_64-Minimal-1810<br>
## 1. Chuẩn bị

- Phiên bản CentOS: `CentOS-7-x86_64-Minimal-1810`
- Môi trường cài đặt: `WMware ESXi`. Ngoài ra có thể cài đặt trên máy vật lý hoặc các môi trường ảo hóa khác như KVM, WMware workstation, Hyper-V.
- Một máy với cấu hình tối thiểu 1 vCPU, 1 GB RAM, 10 GB Disk

## 2. Chuẩn bị file cài CentOS 7.6 - 64 bit

Có rất nhiều nguồn để download file ISO CentOS 7 để cài đặt. Bạn thực hiện tải file ISO CentOS 7 `CentOS-7-x86_64-Minimal-1810.iso`

## 3. Thiết lập môi trường cài đặt

- Nếu như bạn cài đặt trên máy vật lý bạn cần chuẩn bị USB hoặc phương pháp để gắn file ISO vừa down ở trên.

- Trong hướng dẫn này thực hiện trên môi trường ESXi.

## 4. Các bước cài đặt

+ Start máy ảo: Khi khởi động máy ảo lên bạn sẽ nhìn thấy màn hình cài đặt đầu tiên của CentOS 7. Bạn có thể `Enter` để tiến hành cài đặt ngay hoặc nhờ để tự động vào chế độ cài đặt.

![](/images/img-caidat-centos7/Screenshot_728.png)

+ Lựa chọn ngôn ngữ `Tiếng Anh`

![](/images/img-caidat-centos7/Screenshot_729.png)

+ Lựa chọn `Date & Time`

![](/images/img-caidat-centos7/Screenshot_730.png)

![](/images/img-caidat-centos7/Screenshot_731.png)

+ Cấu hình `Network Interface`

Khi bạn vào mục này sẽ hiển thị cho bạn tất cả interface mà server bạn hiện có, bạn có thể bật lên hoặc không.

![](/images/img-caidat-centos7/Screenshot_732.png)

![](/images/img-caidat-centos7/Screenshot_733.png)

+ Cấu hình `Disk`

Ở mục này bạn có thể lựa chọn chế độ tự động phân chia phân vùng hoặc bạn chia phần vùng theo yêu cầu của bạn. Ở đây tôi lựa chọn chế độ tự động.

![](/images/img-caidat-centos7/Screenshot_734.png)

![](/images/img-caidat-centos7/Screenshot_735.png)

+ Bắt đầu cài đặt

![](/images/img-caidat-centos7/Screenshot_736.png)

+ Thiết lập password root cho server

![](/images/img-caidat-centos7/Screenshot_737.png)

![](/images/img-caidat-centos7/Screenshot_738.png)

**Lưu ý:** Bạn đặt password cho server có độ mạnh cao để bảo vệ server khỏi các cuộc tấn công dò quét mật khẩu.

+ Quá trình cài đặt đang diễn ra

![](/images/img-caidat-centos7/Screenshot_739.png)

+ Khởi đông lại để hoàn tất quá trình cài đặt.

![](/images/img-caidat-centos7/Screenshot_740.png)

![](/images/img-caidat-centos7/Screenshot_741.png)

## 5. Kiểm tra

+ Bạn sử dụng tài khoản root, password bạn thiết lập để login vào server CentOS 7 vừa cài đặt và kiểm tra thông số về server của mình.

![](/images/img-caidat-centos7/Screenshot_742.png)

Như vậy bạn đã cài đặt thành công hệ điều hành CentOS 7. Bạn có thể cài đặt tiếp theo các ứng dụng mà bạn mong muốn.

---
Thực hiện bởi [cloud365.vn](https://cloud365.vn/)
