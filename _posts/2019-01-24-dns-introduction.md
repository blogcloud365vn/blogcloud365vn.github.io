---
date: 2019-01-23
title: Giới thiệu cơ bản về DNS
categories:
  - Linux
description: Giới thiệu cơ bản về DNS
author: tuanda
tags: [DNS, Linux, Windows]
type: Document
---

## Giới thiệu

**DNS (Domain Name System)**

Toàn bộ máy tính trên Internet, từ smart phone, laptop, PC đến các server phục vụ các service như websites, mail,.. đều giao tiếp với nhau thông qua địa chỉ IP. Tuy nhiên, địa chỉ IP này đối với các website có thể khác nhau và khó ghi nhớ đối với người dùng.
Hệ thống DNS nắm vai trò như một cuốn "danh bạ" để đối chiếu với tên miền và trả lại cho người dùng IP của máy chủ.

<span style="display:block;text-align:center">![](/images/img-dns/dns_4.jpg)</span>

Địa chỉ IP của một tên miền cụ thể có thể kiểm tra thông qua việc sử dụng lệnh `ping` trong `cmd` (Windows):

<span style="display:block;text-align:center">![](/images/img-dns/dns_2.png)</span>


(Trong ví dụ, tên miền <a href="https://cloud365.vn" target="_blank">cloud365.vn</a> có IP là `103.101.161.201`). 

## Tên miền

<span style="display:block;text-align:center">![](/images/img-dns/dns_3.jpg)</span>

- **Tên miền gốc (root domain)**: 
Nó là đỉnh của nhánh cây của tên miền. Nó có thể biểu diễn đơn giản chỉ là dấu chấm "."

- **Tên miền cấp cao nhất - Top-level domain (TLD)** : 
Top-level domain nằm ở đầu phân cấp về tên miền. ICANN (Internet Corporation for Assigned Names and Numbers) là tổ chức được cấp quyền kiểm soát quản lý đối với các tên miền cấp cao nhất. Sau đó có thể phân phối tên miền bên dưới TLD, thường thông qua một công ty đăng ký tên miền (domain registrar).
Top-level domain là phần xa nhất ở bên phải (được phân tách bằng dấu chấm). Các tên miền cấp cao phổ biến là "com", "net", "org", "gov", "edu",..
    - Com: Tên miền này được dùng cho các tổ chức thương mại.
    - Edu: Tên miền này được dùng cho các cơ quan giáo dục, trường học.
    - Net: Tên miền này được dùng cho các tổ chức mạng lớn.
    - Gov: Tên miền này được dùng cho các tổ chức chính phủ.
    - Org: Tên miền này được dùng cho các tổ chức khác.
    - Info: Tên miền này dùng cho việc phục vụ thông tin.
    - Arpa: Tên miền ngược.

- **Host** : 
Với tên miền, chủ sở hữu có thể tham chiếu đến các máy tính hoặc dịch vụ riêng biệt. Chẳng hạn, hầu hết chủ sở hữu tên miền làm cho máy chủ web của họ có thể truy cập được thông qua tên miền (cloud365.vn) và cũng thông qua định nghĩa "máy chủ" "www" (www.cloud365.vn).

- **Tên miền con (Subdomain)** : 
DNS hoạt động theo thứ bậc. TLD có thể có nhiều tên miền bên dưới. Chẳng hạn, TLD "com" có cả "google.com" và "nhanhoa.com" bên dưới nó. "Tên miền con" là tên miền thuộc một phần của tên miền lớn hơn. Trong trường hợp này, "nhanhoa.com" có thể được coi là tên miền con của "com". Phần "nhanhoa" được gọi là SLD (second level domain), có nghĩa là tên miền cấp hai.

Hiển thị của tên miền trên thanh URL trình duyệt:

<span style="display:block;text-align:center">![](/images/img-dns/dns_2.jpg)</span>

## Các khái niệm trong hệ thống DNS

**Zone File** : 
Zone files được coi là thành phần chính của một Name Server. Nó chứa các thông tin về các hosts, server trong domain. Các thông tin này sẽ được các name server software sử dụng để thực hiện các tác vụ đc yêu cầu. Tính đúng đắn của việc phân tích một request của name server phụ thuộc vào nội dung của các zone files.

**Bản ghi (record)** :
Trong một zone, bản ghi được lưu giữ dùng để ánh xạ một tên miền thành một địa chỉ IP, xác định máy chủ phân giải cho tên miền, xác định máy chủ mail cho tên miền, v.v.

**Tên miền (Domain) vs Zone**
Tên miền được chia thành các zone - thuộc quyền quản lý của các máy chủ DNS.

- Domain đại diện cho toàn bộ tên miền / máy chủ thuộc cùng một tổ chức.
Ví dụ: tất cả các tên miền kết thúc bằng ".com" là một phần của tên miền "com".

- Zone là một tên miền hoặc ít hơn tùy thuộc vào việc ủy quyền (delegated) tên miền con với những máy chủ DNS khác.
Ví dụ: Máy chủ DNS có thể quản lý tất cả các bản ghi trong miền "cloud365.vn", nhưng bằng cách xác định các bản ghi NS cho "abc.cloud365.vn", phần này của miền được ủy quyền cho các máy chủ DNS khác - và có thể một công ty / thực thể khác nhau.
Một zone chứa một bản ghi SOA mô tả các thuộc tính chung của vùng (zones) đó.


Authoritative Name Servers
Each zone is served by at least one authoritative name server, which contains the complete data for the zone. To make the DNS tolerant of server and network failures, most zones have two or more authoritative servers, on different networks.

Responses from authoritative servers have the "authoritative answer" (AA) bit set in the response packets. This makes them easy to identify when debugging DNS configurations using tools like dig (the section called “Diagnostic Tools”).

The Primary Master
The authoritative server where the master copy of the zone data is maintained is called the primary master server, or simply the primary. Typically it loads the zone contents from some local file edited by humans or perhaps generated mechanically from some other local file which is edited by humans. This file is called the zone file or master file.

In some cases, however, the master file may not be edited by humans at all, but may instead be the result of dynamic update operations.

Slave Servers
The other authoritative servers, the slave servers (also known as secondary servers) load the zone contents from another server using a replication process known as a zone transfer. Typically the data are transferred directly from the primary master, but it is also possible to transfer it from another slave. In other words, a slave server may itself act as a master to a subordinate slave server.

Stealth Servers
Usually all of the zone's authoritative servers are listed in NS records in the parent zone. These NS records constitute a delegation of the zone from the parent. The authoritative servers are also listed in the zone file itself, at the top level or apex of the zone. You can list servers in the zone's top-level NS records that are not in the parent's NS delegation, but you cannot list servers in the parent's delegation that are not present at the zone's top level.

A stealth server is a server that is authoritative for a zone but is not listed in that zone's NS records. Stealth servers can be used for keeping a local copy of a zone to speed up access to the zone's records or to make sure that the zone is available even if all the "official" servers for the zone are inaccessible.

A configuration where the primary master server itself is a stealth server is often referred to as a "hidden primary" configuration. One use for this configuration is when the primary master is behind a firewall and therefore unable to communicate directly with the outside world.

Caching Name Servers
The resolver libraries provided by most operating systems are stub resolvers, meaning that they are not capable of performing the full DNS resolution process by themselves by talking directly to the authoritative servers. Instead, they rely on a local name server to perform the resolution on their behalf. Such a server is called a recursive name server; it performs recursive lookups for local clients.

To improve performance, recursive servers cache the results of the lookups they perform. Since the processes of recursion and caching are intimately connected, the terms recursive server and caching server are often used synonymously.

The length of time for which a record may be retained in the cache of a caching name server is controlled by the Time To Live (TTL) field associated with each resource record.

Forwarding
Even a caching name server does not necessarily perform the complete recursive lookup itself. Instead, it can forward some or all of the queries that it cannot satisfy from its cache to another caching name server, commonly referred to as a forwarder.

There may be one or more forwarders, and they are queried in turn until the list is exhausted or an answer is found. Forwarders are typically used when you do not wish all the servers at a given site to interact directly with the rest of the Internet servers. A typical scenario would involve a number of internal DNS servers and an Internet firewall. Servers unable to pass packets through the firewall would forward to the server that can do it, and that server would query the Internet DNS servers on the internal server's behalf.

Name Servers in Multiple Roles
The BIND name server can simultaneously act as a master for some zones, a slave for other zones, and as a caching (recursive) server for a set of local clients.

However, since the functions of authoritative name service and caching/recursive name service are logically separate, it is often advantageous to run them on separate server machines. A server that only provides authoritative name service (an authoritative-only server) can run with recursion disabled, improving reliability and security. A server that is not authoritative for any zones and only provides recursive service to local clients (a caching-only server) does not need to be reachable from the Internet at large and can be placed inside a firewall.


Các loại bản ghi - Resource Record (RR) 
1. SOA (Start of Authority) : Trong mỗi tập tin cơ sở dữ liệu DNS phải có một và chỉ một record SOA (Start of Authority). Bao gồm các thông tin về domain trên DNS Server, thông tin về zone transfer.

Cú pháp :
```sh
[tên miền] IN SOA [tên-server-dns] [địa-chỉ-email] (
                                  2014090401    ; serial
                                        3600    ; refresh
                                        1800    ; retry
                                      604800    ; expire
                                       86400 )  ; minimum
```
`Serial` : áp dụng cho mọi dữ liệu trong zone và có định dạng `YYYYMMDDNN` với YYYY là năm, MM là tháng, DD là ngày, NN là số lần sửa đổi dữ liệu zone trong ngày. Luôn luôn phải tăng số này lên mỗi lần sửa đổi dữ liệu zone. Khi máy chủ Secondary liên lạc với máy chủ Primary, trước tiên nó sẽ hỏi số serial. Nếu số serial của máy Secondary nhỏ hơn số serial của máy Primary tức là dữ liệu zone trên Secondary đã cũ và sao đó máy Secondary sẽ sao chép dữ liệu mới từ máy Primary thay cho dữ liệu đang có.

`Refresh` : chỉ ra khoảng thời gian máy chủ Secondary kiểm tra sữ liệu zone trên máy Primary để cập nhật nếu cần. Giá trị này thay đổi tùy theo tuần suất thay đổi dữ liệu trong zone.

`Retry` : nếu máy chủ Secondary không kết nối được với máy chủ Primary theo thời hạn mô tả trong `refresh` (ví dụ máy chủ Primary bị shutdown vào lúc đó thì máy chủ Secondary phải tìm cách kết nối lại với máy chủ Primary theo một chu kỳ thời gian mô tả trong `retry`. Thông thường, giá trị này nhỏ hơn giá trị `refresh`).

`Expire` : nếu sau khoảng thời gian này mà máy chủ Secondary không kết nối được với máy chủ Primary thì dữ liệu zone trên máy Secondary sẽ bị quá hạn. Khi dữ liệu trên Secondary bị quá hạn thì máy chủ này sẽ không trả lời mỗi truy vấn về zone này nữa. Giá trị `expire` này phải lớn hơn giá trị `refresh` và giá trị `retry`.

`TTL` (time to live) : giá trị này áp dụng cho mọi record trong zone và được đính kèm trong thông tin trả lời một truy vấn. Mục đích của nó là chỉ ra thời gian mà các máy chủ name server khác cache lại thông tin trả lời. Việc cache thông tin trả lời giúp giảm lưu lượng truy vấn DNS trên mạng.
2. NS (Name Server) : Record tiếp theo cần có trong zone là NS (name server) record. Mỗi name server cho zone sẽ có một NS record. Chứa địa chỉ IP của DNS Server cùng với các thông tin về domain đó.

Cú pháp :
[domain_name] IN NS [DNS-Server_name]
Ví dụ : Record NS sau :
Matbao.com. IN NS ns1.matbao.com.
Matbao.com. IN NS ns2.matbao.com.
Chỉ ra hai name servers cho miền matbao.com.

1. Record A

Là một record căn bản và quan trọng, dùng để ánh xạ từ một domain thành địa chỉ IP cho phép có thể truy cập website. Đây là chức năng cốt lõi của hệ thống DNS. Record A có dạng như sau:

example.com   A    123.30.108.142

Qúy khách cũng có thể tạo bản ghi A cho subdomain muốn truy cập đến hosting – VPS – Server.

sub.example.com   A   123.30.108.142

Qúy Khách có thể trỏ subdomain khác nhau đến những IP khác nhau

sub1.example.com   A   123.30.108.150

2. Record CNAME (Canonical Name)

Cho phép tên miền có nhiều bí danh khác nhau, khi truy cập các bí danh sẽ cũng về một địa chỉ tên miền. Để sử dụng bản ghi CNAME cần khai báo bản ghi A trước.

www   CNAME   example.com

mail CNAME example.com

example.com   A   123.30.108.142

Khi một yêu cầu đến địa chỉ alias.com thì DNS sẽ tìm đến  example thông qua bản ghi CNAME, một DNS mới sẽ tiếp tục tìm đến địa chỉ IP: 123.30.108.142 thông qua bản ghi A.

3. Record DKIM

Là bản ghi dùng để xác thực người gửi bằng cách mã hóa một phần email gửi bằng một chuỗi ký tự, xem như là chữ ký.

Khi email được gửi đi máy chủ mail sẻ kiểm so sánh với thông tin bản ghi đã được cấu hình trong DNS để xác nhận. Bản ghi DKIM có dạng:

        mail._domainkey.123host.vn     TXT  k=rsa;p=MIIBIjANBgkqhkiG9w0BA

4. Record MX

Bản ghi MX có tác dụng xác định, chuyển thư đến domain hoặc subdomain đích. Bản ghi MX có dạng

example.com    MX    10    mail.example.com.
mail.example.com    A    123.30.108.142

Mức độ yêu tiên của được đánh từ 0 đến 10, độ ưu tiền càng cao thì số càng thấp.

example.com MX 10 mail_1.example.com
example.com MX 20 mail_2.example.com
example.com MX 30 mail_3.example.com

Bản ghi MX không nhất thiết phải trỏ đến hosting – VPS- Server của Khách hàng. Nếu Khách hàng đang sử dụng dịch vụ mail của bên thứ ba như google app, Khách hàng nên sử dụng bản nghi MX do họ cung cấp.

5. Record SPF

Record SPF được tạo ra nhầm đảm bảo các máy chủ mail sẽ chấp nhận mail từ tên miền của khách hàng chỉ được gửi đi từ server của khách hàng. Sẽ giúp chống spam và giả mạo email. Bản ghi SPF thể hiện dưới dạng:

example.com   TXT     “v=spf1 a ~all”

Với bản ghi SPF, máy chủ tiếp nhận mail sẽ kiểm tra IP của máy chủ gửi và IP của máy chủ đã đăng kí bản ghi SPF example.com. Nếu Khách hàng có nhiều máy chủ mail nên liệt kê tất cả trong bản ghi SPF giúp đảm bảo thư đến được chính xác và đầy đủ.



dig cloud365.vn
dig cloud365.vn ns
dig cloud365.vn soa
dig cloud365.vn soa +short
dig cloud365.vn any
dig -x 103.28.36.11 +short
dig @8.8.8.8 cloud365.vn any 
dig @8.8.8.8 mail.cloud365.vn +trace




max-cache-size 




https://www.dns-school.org/Documentation/bind-arm/Bv9ARM.ch01.html
ftp://ftp.isc.org/www/bind/arm95/Bv9ARM.ch06.html#acache
https://securitydaily.net/tim-hieu-he-thong-ten-mien-dns-domain-name-system/
---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>




