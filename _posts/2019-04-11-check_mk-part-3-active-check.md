---
title: CHECKMK part 3 - Active check và cách cấu hình.
categories:
  - Monitor
description: Tìm hiểu về omd-checkmk.
author: datpt
tags: [monitoring, Linux]
type: Document
set: Gioi-thieu-checkmk
set_order: 23
---

## Lời mở đầu.

Ở bài trước mình đã hướng dẫn mọi người cài đặt checkmk agent để thu thập các metrics giám sát, tuy nhiên đó là những thông số thông qua agent thu thập, nó có thể ít quá hoặc nhiều quá những thông số mà chúng ta cần, hôm nay mình giới thiệu về aactive check - một phương pháp check khác giúp mọi người có nhiều hơn những phương án để giám sát được đầy đủ những thông số cần thiết.

## Chuẩn bị.

- Một máy chủ đã được cài đặt checkmk, nếu chưa có thì cài theo [part 1](https://blog.cloud365.vn/monitor/check_mk-part1-Tong-quan-ve-checkmk-va-vai-dat/)

- Một hoặc nhiều máy chủ client để đẩy metrics về checkmk.

## Thực hiện.

Trên `Web UI`, chúng ta tìm đến `WATO Configuration`, chọn `Host & Services Parameters` :

![omd-20](/images/img-omd/omd-20.png)

Ở đây, có khá nhiều dịch vụ có thể sử dụng Active check, ở đây mình sẽ check dịch vụ SSH.

![omd-21](/images/img-omd/omd-21.png)

Chọn `Create rule in folder` :

![omd-22](/images/img-omd/omd-22.png)

Sau đó cấu hình các thông số về mô tả và các tùy chọn thêm với dịch vụ SSH:

![omd-23](/images/img-omd/omd-23.png)

Cấu hình các host sử dụng dịch vụ SSH, đây là tên host lúc chúng ta đặt tên khi thêm mới host giám sát:

![omd-24](/images/img-omd/omd-24.png)

Sau đó chọn `Save` để lưu lại :

![omd-25](/images/img-omd/omd-25.png)

Lúc này checkmk đã có những thay đổi trong cấu hình monitor, chọn `Changes` để xem những thay đổi đó :

![omd-26](/images/img-omd/omd-26.png)

Sau đó chọn active để lưu lại những thay đổi trên, vào trong Host để kiểm tra dịch vụ moiws thêm:

![omd-27](/images/img-omd/omd-27.png)


## Tổng kết.

Ở bài viết này mình đã giới thiệu cho mọi người về active check, ở bài viết sau mình sẽ giới thiệu về threshold trong checkmk, đây chính là một trong những phần quan trọng trước khi chúng ta cấu hình cảnh báo cho những sự kiện giám sát trên checkmk.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>