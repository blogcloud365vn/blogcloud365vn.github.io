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
Toàn bộ máy tính trên Internet, từ smart phone, laptop, PC đến các server phục vụ các service như websites, mail,.. đều giao tiếp với nhau thông qua địa chỉ IP. Tuy nhiên, địa chỉ IP này đối với các website có thể khác nhau và khó ghi nhớ đối với người dùng (ví dụ <a href="https://nhanhoa.com/" target="_blank">nhanhoa.com</a> là `123.30.181.113`). 

Dịch vụ DNS nhằm phiên dịch từ ngôn ngữ thân thiện với người dùng (tên miền) sang ngôn ngữ máy tính sử dụng để giao tiếp với nhau (địa chỉ IP). Người dùng thay vì phải gõ dãy dài IP, chỉ cần gõ tên site vào trình duyệt và có thể truy cập vào website.

![](/images/img-dns/dns_1.png)

## Khái niệm

Trước khi đi vào tìm hiểu hệ thống DNS hoạt động như thế nào, chúng ta cùng đi vào một số khái niệm:

- **Tên miền (Domain)** : 
Tên miền là định danh thân thiện với người dùng, được sử dụng để giao tiếp với các tài nguyên trên mạng internet (ví dụ: <a href="https://nhanhoa.com/" target="_blank">nhanhoa.com</a>, khi bạn gõ tên miền vào URL trình duyệt có thể truy cập được các thông tin của website)

- **Địa chỉ IP** : 
Được sử dụng để giao tiếp giữa các thiết bị trong môi trường internet.

- **Tên miền gốc (Domain root)**: 
Nó là đỉnh của nhánh cây của tên miền. Nó có thể biểu diễn đơn giản chỉ là dấu chấm "."

- **Tên miền cấp cao nhất - Top-level domain (TLD)** : 
Top-level domain nằm ở đầu phân cấp về tên miền. ICANN (Internet Corporation for Assigned Names and Numbers) là tổ chức được cấp quyền kiểm soát quản lý đối với các tên miền cấp cao nhất. Sau đó có thể phân phối tên miền bên dưới TLD, thường thông qua một công ty đăng ký tên miền (domain registrar).
Top-level domain là phần xa nhất ở bên phải (được phân tách bằng dấu chấm). Các tên miền cấp cao phổ biến là "com", "net", "org", "gov", "edu" và "io".
    - Com: Tên miền này được dùng cho các tổ chức thương mại.
    - Edu: Tên miền này được dùng cho các cơ quan giáo dục, trường học.
    - Net: Tên miền này được dùng cho các tổ chức mạng lớn.
    - Gov: Tên miền này được dùng cho các tổ chức chính phủ.
    - Org: Tên miền này được dùng cho các tổ chức khác.
    - Info: Tên miền này dùng cho việc phục vụ thông tin.
    - Arpa: Tên miền ngược.
    - Mã các nước trên thế giới tham gia vào mạng internet, các quốc gia này được qui định bằng hai chữ cái theo tiêu chuẩn ISO-3166. Ví dụ: Việt Nam là .vn, Singapo là sg….

- **Host** : 
Với tên miền, chủ sở hữu có thể tham chiếu đến các máy tính hoặc dịch vụ riêng biệt. Chẳng hạn, hầu hết chủ sở hữu tên miền làm cho máy chủ web của họ có thể truy cập được thông qua tên miền (cloud365.vn) và cũng thông qua định nghĩa "máy chủ" "www" (www.cloud365.vn).

![](/images/img-dns/dns_2.jpg)

- **Tên miền con (Subdomain)** : 
DNS hoạt động theo thứ bậc. TLD có thể có nhiều tên miền bên dưới. Chẳng hạn, TLD "com" có cả "google.com" và "nhanhoa.com" bên dưới nó. "Tên miền con" là tên miền thuộc một phần của tên miền lớn hơn. Trong trường hợp này, "nhanhoa.com" có thể được coi là tên miền con của "com". Phần "nhanhoa" được gọi là SLD (second level domain), có nghĩa là tên miền cấp hai.

- **Tên miền đầy đủ - Fully Qualified Domain Name (FQDN)** : 
Một tên miền đủ (FQDN) là một tên tuyệt đối chỉ định vị trí của nó bao gồm từng miền cha (bao gồm TLD và root domain).
Một ví dụ về FQDN là "support.cloud365.vn.". Dấu chấm vẫn cần phải tuân theo các tiêu chuẩn ICANN.

- **Máy chủ phân giải tên miền (Name Server)** : 
Name server là một máy tính được chỉ định để dịch tên miền thành địa chỉ IP. Các máy chủ này thực hiện hầu hết các công việc trong hệ thống DNS. Vì số lượng xử lý quá nhiều với bất kỳ một máy chủ nào, nên mỗi máy chủ có thể chuyển hướng yêu cầu đến các máy chủ khác hoặc ủy thác cho một nhóm máy chủ phụ trách các tên miền con.

- **Zone File** : 
Zone files đc coi là thành phần chính của một Name Server. Nó chứa các thông tin về các hosts, server trong domain. Các thông tin này sẽ được các name server software sử dụng để thực hiện các tác vụ đc yêu cầu. Tính đúng đắn của việc phân tích một request của name server phụ thuộc vào nội dung của các zone files.

- **Bản ghi (record)** :
Trong một zone, bản ghi được lưu giữ dùng để ánh xạ một tên miền thành một địa chỉ IP, xác định máy chủ phân giải cho tên miền, xác định máy chủ mail cho tên miền, v.v.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>




