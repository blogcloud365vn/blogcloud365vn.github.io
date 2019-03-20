---
date: 2019-03-20
title: Hướng dẫn sử dụng công cụ MTR (My Traceroute)
categories:
  - Linux
description: Hướng dẫn sử dụng công cụ MTR (My Traceroute)
author: duydm
tags: [Beginer, Linux]
type: Document
---

Đối với một người quản trị hệ thống việc giám sát sự hoạt động ổn định của hệ thống là một điều cực ký quan trọng, các hệ thống triển khai dịch vụ online thì vấn đề được đặt lên hàng đầu đó là `network`. Để có thể giám sát được tình trạng online, các package gửi/nhận thông thường quản trị hệ thống sử dụng các command line như `ping`, `traceroute` để xem quá trình truyển tải các package trên mạng qua các điểm có ổn định hay không. Thay vì sử dụng những câu lệnh đơn lẻ và phải tập hợp các thông số rời rạc để phân tích đưa ra nhận định, bạn có thể sử dụng các tool chuyện dụng cho việc phân tích mạng. Sau đây sẽ hướng dẫn các bạn sử dụng công cỵ `mtr` (my traceroute) để thực hiện đánh giá, phân tích trạng thái của network.

![](/images/img-mtr/mtr.png)

`MTR` là một công cụ bao gồm 2 chương trình `traceroute` và `ping`  

















---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>