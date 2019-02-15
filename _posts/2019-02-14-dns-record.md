---
date: 2019-02-14
title: 
categories:
  - Linux
description: Giới thiệu cơ bản về DNS
author: tuanda
tags: [DNS, Linux, Windows]
type: Document
---

Các loại bản ghi - Resource Record (RR) 
## 1. SOA (Start of Authority) 

Trong mỗi tập tin cơ sở dữ liệu DNS phải có một và chỉ một record SOA (Start of Authority). Bao gồm các thông tin về domain trên DNS Server, thông tin về zone transfer.

Cú pháp :
```sh
$TTL 86400
@       IN SOA  masterdns.tuanda.com.     admin.tuanda.com. (
                                  2014090401    ; serial
                                        3600    ; refresh
                                        1800    ; retry
                                      604800    ; expire
                                       86400 )  ; TTL
```
- `masterdns.tuanda.com.` giá trị DNS chính của tên miền hoặc máy chủ.
- `admin.tuanda.com.` chuyển đổi từ dạng `admin@tuanda.com`, thể hiện chủ thể sở hữu tên miền này.

`Serial` : áp dụng cho mọi dữ liệu trong zone và có định dạng `YYYYMMDDNN` với YYYY là năm, MM là tháng, DD là ngày, NN là số lần sửa đổi dữ liệu zone trong ngày. Luôn luôn phải tăng số này lên mỗi lần sửa đổi dữ liệu zone. Khi máy chủ Secondary liên lạc với máy chủ Primary, trước tiên nó sẽ hỏi số serial. Nếu số serial của máy Secondary nhỏ hơn số serial của máy Primary tức là dữ liệu zone trên Secondary đã cũ và sao đó máy Secondary sẽ sao chép dữ liệu mới từ máy Primary thay cho dữ liệu đang có.

`Refresh` : chỉ ra khoảng thời gian máy chủ Secondary kiểm tra sữ liệu zone trên máy Primary để cập nhật nếu cần. Giá trị này thay đổi tùy theo tuần suất thay đổi dữ liệu trong zone.

`Retry` : nếu máy chủ Secondary không kết nối được với máy chủ Primary theo thời hạn mô tả trong `refresh` (ví dụ máy chủ Primary bị shutdown vào lúc đó thì máy chủ Secondary phải tìm cách kết nối lại với máy chủ Primary theo một chu kỳ thời gian mô tả trong `retry`. Thông thường, giá trị này nhỏ hơn giá trị `refresh`).

`Expire` : nếu sau khoảng thời gian này mà máy chủ Secondary không kết nối được với máy chủ Primary thì dữ liệu zone trên máy Secondary sẽ bị quá hạn. Khi dữ liệu trên Secondary bị quá hạn thì máy chủ này sẽ không trả lời mỗi truy vấn về zone này nữa. Giá trị `expire` này phải lớn hơn giá trị `refresh` và giá trị `retry`.

`TTL` (time to live) : giá trị này áp dụng cho mọi record trong zone và được đính kèm trong thông tin trả lời một truy vấn. Mục đích của nó là chỉ ra thời gian mà các máy chủ name server khác cache lại thông tin trả lời. 

## 2. NS (Name Server)  

Record tiếp theo cần có trong zone là NS (name server) record. Mỗi name server cho zone sẽ có một NS record. Chứa địa chỉ IP của DNS Server cùng với các thông tin về domain đó.

Ví dụ : Record NS sau :

```sh
cloud365.vn. IN NS ns1.zonedns.vn.
cloud365.vn. IN NS ns2.zonedns.vn.
```
Chỉ ra hai name servers cho miền Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>.

## 3. Record A

Là một record căn bản và quan trọng, dùng để ánh xạ từ một domain thành địa chỉ IP cho phép có thể truy cập website. Đây là chức năng cốt lõi của hệ thống DNS. Record A có dạng như sau:

`cloud365.vn   A    103.101.161.201`

Tên miền con (subdomain):

`sub.cloud365.vn   A   103.101.161.201`

## 4. Record SRV

Bản ghi SRV được sử dụng để xác định vị trí các dịch vụ đặc biệt trong 1 domain, ví dụ tên máy chủ và số cổng của các máy chủ cho các dịch vụ được chỉ định. Ví dụ:

```sh
_ldap._tcp.example.com. 3600  IN  SRV  10  0  389  ldap01.example.com.
```

Một Client trong trường hợp này có thể nhờ DNS nhận ra rằng, trong tên miền example.com có LDAP Server ldap01, mà có thể liên lạc qua cổng TCP Port 389 .

- Các trường trong record SRV :
    - Tên dịch vụ service.
    - Giao thức sử dụng.
    - Tên miền (domain name).
    - TTL: Thời gian RR được giữ trong cache
    - Class: standard DNS class, luôn là IN
    - Ưu tiên: ưu tiên của host, số càng nhỏ càng ưu tiên.
    - Trọng lượng: khi cùng bực ưu tiên, thì trọng lượng 3 so với trọng lượng 2 sẽ được lựa chọn 60% (hỗ trợ load balancing).
    - Port của dịch vụ (tcp hay udp).
    - Target chỉ định FQDN cho host hỗ trợ dịch vụ.

## 5. Record CNAME (Canonical Name)

Cho phép tên miền có nhiều bí danh khác nhau, khi truy cập các bí danh sẽ cũng về một địa chỉ tên miền. Để sử dụng bản ghi CNAME cần khai báo bản ghi A trước. Ví dụ bản ghi CNAME phổ biến nhất:

```sh
www   CNAME   example.com

mail CNAME example.com

example.com   A   103.101.161.201
```

Khi một yêu cầu đến địa chỉ www.example.com thì DNS sẽ tìm đến  example thông qua bản ghi CNAME, một truy vấn DNS mới sẽ tiếp tục tìm đến địa chỉ IP: 103.101.161.201 thông qua bản ghi A.

## 6. Record MX

Bản ghi MX có tác dụng xác định, chuyển thư đến domain hoặc subdomain đích. Bản ghi MX có dạng

```sh
example.com    MX    10    mail.example.com.
mail.example.com    A    103.101.161.201
```

Độ ưu tiền càng cao thì số càng thấp.

```sh
example.com MX 10 mail_1.example.com
example.com MX 20 mail_2.example.com
example.com MX 30 mail_3.example.com
````

Bản ghi MX không nhất thiết phải trỏ đến hosting – VPS- Server của khách hàng. Nếu khách hàng đang sử dụng dịch vụ mail của bên thứ ba như Gsuite Khách hàng nên sử dụng bản nghi MX do họ cung cấp.

## 7. Record TXT

Bản ghi TXT(text) được sử dụng để cung cấp khả năng liên kết văn bản tùy ý với máy chủ. Chủ yếu dùng trong mục đích xác thực máy chủ với tên miền.

## 8. Record DKIM

Là bản ghi dùng để xác thực người gửi bằng cách mã hóa một phần email gửi bằng một chuỗi ký tự, xem như là chữ ký.

Khi email được gửi đi máy chủ mail sẽ kiểm so sánh với thông tin bản ghi đã được cấu hình trong DNS để xác nhận. Bản ghi DKIM có dạng:

        mail._domainkey.cloud365.vn     TXT  k=rsa;p=MIIBIjANBgkqhkiG9w0BA

## 9. Record SPF

Record SPF được tạo ra nhầm đảm bảo các máy chủ mail sẽ chấp nhận mail từ tên miền của khách hàng chỉ được gửi đi từ server của khách hàng. Sẽ giúp chống spam và giả mạo email. Bản ghi SPF thể hiện dưới dạng:

```sh
cloud365.vn   SPF     "v=spf1 ip4:103.101.162.0/24 -all" 3600
```

Tùy vào hệ thống DNS mà có thể hiển thị bản ghi SPF hoặc TXT
Với bản ghi SPF, máy chủ tiếp nhận mail sẽ kiểm tra IP của máy chủ gửi và IP của máy chủ đã đăng kí bản ghi SPF example.com. Nếu Khách hàng có nhiều máy chủ mail nên liệt kê tất cả trong bản ghi SPF giúp đảm bảo thư đến được chính xác và đầy đủ.  


** Tài liệu tham khảo:
https://vi.wikipedia.org/wiki/SRV_Record#C%E1%BA%A5u_tr%C3%BAc

https://www.dns-school.org/Documentation/bind-arm/Bv9ARM.ch01.html
ftp://ftp.isc.org/www/bind/arm95/Bv9ARM.ch06.html#acache
https://securitydaily.net/tim-hieu-he-thong-ten-mien-dns-domain-name-system/


---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
