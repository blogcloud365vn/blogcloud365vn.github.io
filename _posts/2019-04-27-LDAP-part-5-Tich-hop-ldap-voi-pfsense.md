---
title: LDAP [Part 5] - Tích hợp LDAP với Pfsense
categories:
  - LDAP
description: Tích hợp LDAP với Pfsense
author: huytm
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 5
---

## Lời mở đầu.

Pfsense

## Chuẩn bị.

- Pfsense version pfSense-CE-2.4.4

- OpenLDAP

- PHP ldapadmin

## Mô hình.

![ldap-18](/images/img-ldap-datpt/ldap-18.png)


## Các bước chuẩn bị trên LDAP.


#### Tạo một cấu trúc như sau:

![alt text](/images/img-ldap-datpt/anh1.png)

Tạo group, tùy vào mục đích sử dụng sẽ tạo các group khác nhau, ở đây mình tạo group pfsense_admin: 

- Chọn `ou=group` -> `Create a child entry` -> `Generic: Posix Group` :

![alt text](/images/img-ldap-datpt/anh2.png)
 
- Nhập tên group là `pfsense_admin` và chọn `Create Object`

![alt text](/images/img-ldap-datpt/anh3.png)

- Chọn commit

![alt text](/images/img-ldap-datpt/anh4.png)

#### Tạo user

- Chọn `ou=people` -> `Create a child entry` -> `Generic: User Account`

![alt text](/images/img-ldap-datpt/anh5.png)

- Nhập các thông tin cần thiết sau đó chọn Creat Object

  **First name** : *huy* 
  
  **Last name**  : *tran*
  
  **Common Name**: *huytmcn*
  
  **User ID**    : *huytmuid*
  
  **Password**   :  *****
  
  **GID Number** : *pfsense_admin*
  
 ![alt text](/images/img-ldap-datpt/anh6.png)

- Chọn Commit

![alt text](/images/img-ldap-datpt/anh7.png)

#### Mapping user với group 

> Vì mình sử dụng php ldapadmin nên một số group defaul sẽ cần phải bổ sung thêm attribute.

- Chọn `cn=pfsense_admin` -> `Add new attribute`

![alt text](/images/img-ldap-datpt/anh8.png)

- Chọn `memberUid`

![alt text](/images/img-ldap-datpt/anh9.png)

- Nhập user vửa tạo Sau đó chọn Update Object

![alt text](/images/img-ldap-datpt/anh10.png)

- Chọn Update Object

![alt text](/images/img-ldap-datpt/anh11.png)


> Lần sau muốn map user vào group pfsense_admin thì chỉ cần chọn (add value) và thêm user mong muốn.

Thực hiện tạo các user và group khác tùy mục đích sử dụng của bạn


## Các bước thực hiện trên pfsense

Đăng nhập vào tài khoản admin 

#### Tạo mới một kiểu xác thực sử dụng LDAP 

- Chọn `System` -> `User Manager`

![alt text](/images/img-ldap-datpt/anh12.png)

- Chọn Authentication Server

![alt text](/images/img-ldap-datpt/anh13.png)

- Chọn Add

![alt text](/images/img-ldap-datpt/anh14.png)

- Nhập thông tin và chọn Save

  **Descriptive name**          : *LDAP-authen*
  
  **Type**                      : *LDAP*
  
  **Hostname or IP address**    : *10.10.12.193* (Địa chỉ IP LDAP server)
  
  **Port value**                : *389*
  
  **Transport**                 : *TCP - Standard*
  
  **Peer Certificate Authority**: *Global Root CA List*
  
  **Protocol version**          : *3*
  
  **Server Timeout**            : *25*
  
  **Search scope - Level**      : *Entire Subtree*
  
  **Search scope - BaseDN**     : *Entire Subtree*
  
  **Base DN**                   : *dc=nhanhoa,dc=local*
  
  **Authentication containers** : *ou=people,dc=nhanhoa,dc=local*
  
  **Extended query**            : *Uncheck*
  
  **Bind anonymous**            : *UnCheck*
  
  **Bind credentials**          : *cn=Manager,dc=nhanhoa,dc=local*  (Password *****)
  
  **Initial Template**          : *OpenLDAP*
  
  **User naming attribute**     : *cn*
  
  **Group naming attribute**    : *cn*
  
  **Group member attribute**    : *memberUid*
  
  **RFC 2307 Groups**           : *Check*
  
  **Group Object Class**        : *posixGroup*
  
  **UTF8 Encode**               : *Uncheck*
  
  **Username Alterations**      : *Uncheck*
  
  Chọn `Save`
  
  ![alt text](/images/img-ldap-datpt/anh15.png)
  
#### Thiết lập xác thực bằng LDAP

- Chọn Settings -> Chọn Save & Test

![alt text](/images/img-ldap-datpt/anh18.png)

- Kết quả 

![alt text](/images/img-ldap-datpt/anh19.png)
  
#### Tạo group và phân quyền

Chú ý, Group mapping trong Pfsense phải trùng tên với group trong LDAP

- Chọn Groups -> Chọn Add

![alt text](/images/img-ldap-datpt/anh16.png)

- Nhập thông tin như sau:
  
  **Group name** : *pfsense_admin*
  
  **Scope**      : *Remote*
  
  **Description**: *Xác thực bằng LDAP*
  
  Chọn Save
  
  ![alt text](/images/img-ldap-datpt/anh17.png)
  
- Phân quyền cho Group pfsense_admin:

![alt text](/images/img-ldap-datpt/anh22.png)

- Chọn Add 

![alt text](/images/img-ldap-datpt/anh23.png)

- Chọn WebCfg- All pages -> Chọn Save -> Chọn Save tiếp 1 lần nữa 

![alt text](/images/img-ldap-datpt/anh24.png)

#### Kiểm tra lại cấu hình 

- Chọn `Diagnostics` -> `Authentication`

![alt text](/images/img-ldap-datpt/anh20.png)

- Nhập thông tin như sau 
  
  **Authentication Server** : LDAP-authen
  **Username**              : huytmcn
  **Password**              : ****** 

  Chọn Test 
  
- Kết quả 

![alt text](/images/img-ldap-datpt/anh21.png)

## Kiểm tra lại

Login vào pfsense với tài khoản LDAP


Cảm ơn đã truy cập vào Cloud365 !


## Tổng kết.

Ở bài này mình đã hướng dẫn mọi người cách tích hợp LDAP với PFsense, ở các bài viết sau mình sẽ hướng dẫn tích hợp với một số hệ thống khác trên CentOS 7.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>