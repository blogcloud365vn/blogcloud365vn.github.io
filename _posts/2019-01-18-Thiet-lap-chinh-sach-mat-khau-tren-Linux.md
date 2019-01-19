---
title: Thiết lập mật khẩu an toàn trên Linux
categories:
  - Linux
description: Tài liệu thiết lập mật khẩu an toàn trên Linux
author: datpt
tags: [Security, Password]
type: Document
---


**Lời mở đầu**

- Hiện nay có rất nhiều các bài viết hướng dẫn về tạo chính sách mật khẩu trên Windows, tuy nhiên thì các chính sách về mật khẩu trên Linux 
thì không có nhiều, hôm nay mình sẽ giới thiệu về chính sách mật khẩu an toàn trên Linux để chúng ta có thể bảo đảm an toàn cho các tài khoản cũng như máy chủ của mình.



## Các chính sách mật khẩu an toàn trên Linux.

- Tất cả các thiết lập về mật khẩu an toàn trên Linux đều được lưu tại `/etc/login.defs`, mở file `/etc/login.defs` và thực hiện các chính sách về mật khẩu :

    ```sh
    vi /etc/login.defs
    ```

## 1. Thiết lập số ngày hế t hạn của mật khẩu

- Với thiết lập này thì các users phải thay đổi mật khẩu của họ khi mật khẩu dùng tới giới hạn mà chúng ta đã thiết lập.

- Tại dòng 25, đặt số ngày mà chúng ta muốn thiết lập, ở đây là 60:

    ```sh
    PASS_MAX_DAYS 60
    ```
## 2. Thiết lập thời gian tồn tại của mật khẩu

- Với thiết lập này thì khi bạn thay đổi mật khẩu hay đặt mật khẩu cho user, mật khẩu đó sẽ tồn tại trong khoảng thời gian mà 
chúng ta quy định sau đó mới có thể đổi mật khẩu:

- Tại dòng 26, đặt thời gian mật khẩu tồn tại, ở đây là 2 ngày:

    ```sh
    PASS_MIN_DAYS 2
    ```

## 3. Cảnh báo thời gian hết hạn

- Thiết lập số ngày cảnh báo trước khi mật khẩu hết hạn. Thiết lập này chỉ áp dụng đối với user được tạo mới, không có tác dụng đối với user đang tồn tại trên hệ thống. Đối với user đang tồn tại trên hệ thống chúng ta sử dụng câu lệnh `change -W <số ngày> <user>`

- Tại dòng 28 thay đổi số ngày trước khi hết hạn nhận cảnh báo, ở đây là 7:

    ```sh
    PASS_WARN_AGE 7
    ```

## 4. Giới hạn mật khẩu đã được đặt trước đó

- Giới hạn người dùng sử dụng password đã được đặt trước đó. Người dùng không thể thiết lập password quá số lượt quy định.

- Mở file `/etc/pam.d/system-auth`:

    ```sh
    vi /etc/pam.d/system-auth
    ```

- Cạnh dòng 15, đặt số lần mật khẩu đó được thiết lập:

    ```sh
    password     sufficient     pam_unix.so sha512 shadow nullok try_first_pass
    use_authtok remember=5
    ```

## 5. Thiết lập độ dài mật khẩu ngắn nhất

- Thiết lập độ dài ngắn nhất của mật khẩu. Người dùng không thể đặt mật khẩu ngắn hơn số ký tự quy định.

- Thiết lập mật khẩu ngắn nhất là 8 kí tự:

    ```sh
    authconfig --passminlen=8 --update
    ```

## 6. Thiết lập độ phức tạp của mật khẩu theo lớp

- Trong Linux có các class kí tự như sau : UpperCase / LowerCase / Digits / Others. Độ phức tạp của mật khẩu được thiết lập theo số class xuất hiện trong mật khẩu.

- Cấu hình mật khẩu phải xuất hiện ít nhất 2 class:

    ```sh
    authconfig --passminclass=2 --update
    ```

## 7. Thiết lập số lần lặp kí tự

- Thiết lập số lần lặp tối đa của một ký tự liền kề  đối với password mới.

- Cấu hình tối da cho phép 2 kí tự lặp nhau:

    ```sh
    authconfig --passmaxrepeat=2 --update
    ```

## 8.Thiết lập độ phức tạp đối với mật khẩu mới.

- Yêu cầu có ít nhất một ký tự thường :

    ```sh
    authconfig --enablereqlower --update
    ```

- Yêu cầu có ít nhất một ký tự in hoa :

    ```sh
    authconfig --enablerequpper --update
    ```

- Yêu cầu có ít nhất một ký tự số :

    ```sh
    authconfig --enablereqdigit --update
    ```

## 9. Thiết lập độ dài monotonic.

- Monotonic trong toán học được gọi là hàm số đơn điệu, là một chuỗi các số tăng hoặc giảm trong khoảng. Ở đây cấu hình hình có thể được hiểu là các ký tự tăng hoặc giảm trong khoảng được định sẵn, ví dụ thiết lập là 5 thì chỉ có thể đặt mật khẩu là `12345` hoặc `abcdf` chứ không thể đặt chuỗi dài hơn được.

- Mở file `/etc/security/pwquality.conf`:

    ```sh
    vi /etc/security/pwquality.conf
    ```

- Thêm vào cuối file dòng cấu hình

    ```sh
    maxsequence = 5
    ```

## 10. Thiết lập số kí tự trong mật khẩu mới không được có trong mật khẩu cũ.

- Để thiết lập số kí tự trong mật khẩu mới không được có trong mật khẩu cũ chúng ta làm như sau.

- Đầu tiên, mở file `/etc/security/pwquality.conf`:

    ```sh
    vi /etc/security/pwquality.conf
    ```

- Thêm vào cuối file dòng cấu hình sau:

    ```sh
    difok = 5
    ```

## 11. Cấu hình danh sách các ký tự không được xuất hiện trong mật khẩu.

- Để thiết lập danh sách các ký tự không được xuất hiện trong mật khẩu, chúng ta làm như sau.

- Đầu tiên, mở file `/etc/security/pwquality.conf`:

    ```sh
    vi /etc/security/pwquality.conf
    ```

- Thêm vào cuối file dòng cấu hình với những từ không được phép xuất hiện trong mật khẩu:

    ```sh
    badwords = denywords1 denywords2 denywords3
    ```

## 12. Cấu hình thuật toán băm/mã hóa cho mật khẩu mới.

- Để thiết lập thuật toán băm/mã hóa cho mật khẩu mới chúng ta làm như sau.

- Đầu tiên, kiểm tra thuật toán đang dùng hiện tại :

    ```sh
    authconfig --test | grep hashing
    ```

- Thay đổi sang thuật toán mong muốn sử dụng, ở đây là SHA512

    ```sh
    authconfig --passalgo=sha512 --update
    ```

---
Thực hiện bởi [cloud365.vn](https://cloud365.vn/)
