---
date: 2019-04-08
title: Tìm hiểu LVM   
categories:
  - LINUX
description: Tìm hiểu tổng quan về công nghệ LVM 
author: anhduc
tags: [LVM,LINUX]
type: Document
---

## Lời mở đầu 
Phân chia phân vùng sao cho hợp lý. Cần bao nhiêu GB cho một phân vùng đó là một câu hỏi không thể trả lời được khi ta cài lại hệ điều hành. Vậy làm thế nào để ta có thể tạo mở rộng phân vùng này và thu hẹp phân vùng khác. Thì tôi sẽ giới thiệu cho bạn một phương pháp để có thể làm được việc đó. Đó là Logical Volume Manager (LVM)

## Tổng quan 
1. Khái niệm 
Logical Volume Manager (LVM) : LVM là kỹ thuật quản lý việc thay đổi kích thước lưu trữ của ổ cứng. Là một phương pháp ấn định không gian ổ đĩa thành những logicalvolume khiến cho việc thay đổi kích thước của một phân vùng trở nên dễ dàng. Điều này thật dễ dàng khi bạn muốn quản lý công việc của mình tại riêng một phân vùng mà muốn mở rộng nó ra lớn hơn.

Một số khái niệm liên quan: 
- Physical volume: là một đĩa cứng vật lý hoặc là partition
- Volume group: là một nhóm các physical volume ( ổ đĩa ảo )
- logical volume: là các phân vùng ảo của ổ đĩa ảo

Một số lệnh cần thiết: 
- Lệnh `fdisk` : Dùng để  quản lý việc phân vùng trong ổ cứng. Là một công cụ hữu dụng tron linux [tìm hiểu thêm FDISK](https://github.com/duckmak14/thuctapsinh/blob/master/Anhduc/liunux/docs/fdisk.md)
- Lệnh `mount` : Dùng để gắn một phân vùng vào thư mục root để có thể sử dụng được nó [tìm hiểu thêm về mount](https://github.com/duckmak14/thuctapsinh/blob/master/Anhduc/liunux/docs/mount.md)
- Lệnh `dd` : Dùng Sao lưu và hồi phục toàn bộ dữ liệu ổ cứng hoặc một partition và kiểm tra tốc độ đọc của kiểu lưu trữ dữ liệu trong LVM
2. Ưu điểm và Nhược điểm của LVM 
Ưu điểm :
- Không để hệ thống bị gián đoạn hoạt động 
- Không làm hỏng dịch vụ
- Có thể kế hợp swap
- Có thể tạo ra các vùng dung lượng lớn nhỏ tuỳ ý.

Nhược điểm: 
- Các bước thiết lập phức tạp và khó khăn hơn
- Càng gắn nhiều đĩa cứng và thiết lập càng nhiều LVM thì hệ thống khởi động càng lâu.
- Khả năng mất dữ liệu cao khi một trong số các đĩa cứng bị hỏng.
- Windows không thể nhận ra vùng dữ liệu của LVM. Nếu Dual-boot ,Windows sẽ không thể truy cập dữ liệu trong LVM.
3. Những thành phần trong LVM 
- HDD : là một thiết bị lưu trữ máy tính  