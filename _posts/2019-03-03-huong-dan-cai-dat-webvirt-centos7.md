---
title: Hướng dẫn cài đặt Webvirtmgr trên CentOS 7
categories:
  - Linux
description: Tài liệu hướng dẫn cài đặt Webvirtmgr trên CentOS 7
author: niemdt
tags: [KVM, Linux, Centos]
type: Document
---

## Tổng quan

Webvirtmgr là một trong những công cụ quản lý máy ảo KVM. Nó có chức năng gần giống với virt-manager khi có thể kết nối đến nhiều host KVM để có thể quản lý tập trung các VM trên các máy đó. Nhưng nó có một ưu điểm đặc biệt hơn so với virt-manager đó là với virt-manager đó là ta chỉ có thể làm việc tại máy cài virt-manager. Còn với Webvirtmgr ta có thể làm việc với các VM ở bất kỳ đâu có internet. 

## Chuẩn bị

- Một máy cài đặt hệ điều hành CentOS 7 có cấu hình tối thiểu 1 CPU, 1 GB RAM và có 1 card mạng.

- Ít nhất một máy CentOS 7 đã cài đặt KVM để kiểm tra lại webvirtmgr đã hoạt động.

## Mô hình

![](/images/img-cai-dat-webvirtmgr/1.png)

Vì đây là môi trường lap nên tôi dùng địa chỉ IP private nên chỉ có thể quản truy cập vào Webvirtmgr trong môi trường mạng LAN. Nếu bạn muốn truy cập vào Webvirtmgr ở bất kỳ đâu thì bạn Server cài Webvirt của bạn cần có IP public.

Trong môi trường lab nên tôi tiến hành tắt firewalld trên server cài Webvirtmgr `systemctl stop firewalld`

## Server cài Webvirtmgr

**Bước 1: Cài một số gói cần thiết**

```
yum install epel-release

yum -y install git python-pip libvirt-python libxml2-python python-websockify supervisor nginx cyrus-sasl-md5

yum -y install gcc python-devel

pip install --upgrade pip

pip install numpy
```

**Bước 2: Clone Webvirtmgr từ GitHub và cài đặt**

```
git clone git://github.com/retspen/webvirtmgr.git

cd webvirtmgr

pip install -r requirements.txt
```

Sau đó ta chạy lệnh để thiết lập môi trường Django và tạo ra một tài khoản để đăng nhập Webvirtmgr

```
./manage.py syncdb
```

Nó sẽ hiển thị để cho ta nhập một số thông số cần thiết như username, địa chỉ E-mail, và password

```
You just installed Django's auth system, which means you don't have any superusers defined.

Would you like to create one now? (yes/no): yes

Username (leave blank to use 'root'): 

Email address: 

Password: 

Password (again): 

Superuser created successfully.

Installing custom SQL ...

Installing indexes ...

Installed 6 object(s) from 1 fixture(s)
```

Sau đó tiếp tục chạy lệnh

```
./manage.py collectstatic
```

Move thư mục `webvirtmgr` và trong thư mục `/var/www`

```
mkdir /var/www

cd ../

mv webvirtmgr /var/www/
```

**Bước 3: Cài đặt nginx**

Mở file `webvirtmgr` (ban đầu file này chưa tồn tại câu lệnh bên dưới cũng tiến hành tạo file)

```
vi /etc/nginx/conf.d/webvirtmgr.conf
```

Thêm vào file những dòng như sau

```
server {
listen 80 default_server;
 
server_name $hostname;
access_log /var/log/nginx/webvirtmgr_access_log;
 
location /static/ {
root /var/www/webvirtmgr/webvirtmgr; # or /srv instead of /var
expires max;
}
 
location / {
proxy_pass http://127.0.0.1:8000;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-for $proxy_add_x_forwarded_for;
proxy_set_header Host $host:$server_port;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_connect_timeout 600;
proxy_read_timeout 600;
proxy_send_timeout 600;
client_max_body_size 1024M; # Set higher depending on your needs
}
}
```

Vào file `/etc/nginx/nginx.conf`

```
vi /etc/nginx/nginx.conf
```

Comment lại những dòng sau:

```
# server {
# listen 80 default_server;
# listen [::]:80 default_server;
# server_name _;
# root /usr/share/nginx/html;
#
# # Load configuration files for the default server block.
# include /etc/nginx/default.d/*.conf;
#
# location / {
# }
#
# error_page 404 /404.html;
# location = /40x.html {
# }
#
# error_page 500 502 503 504 /50x.html;
# location = /50x.html {
# }
# }
```

Restart và enable nginx

```
service nginx restart
systemctl enable nginx
```

Cho phép Webvirtmgr khởi động cùng hệ thống

```
/usr/sbin/setsebool httpd_can_network_connect true

chkconfig supervisord on
```

**Bước 4: Setup Supervisor**

Cấp quyền cho người dùng

```
chown -R nginx:nginx /var/www/webvirtmgr
```

Mở file `webvirtmgr.ini` (file này ban đầu cũng chưa tồn tại)

```
vi /etc/supervisord.d/webvirtmgr.ini
```

Thêm vào những dòng sau:

```
[program:webvirtmgr]
command=/usr/bin/python /var/www/webvirtmgr/manage.py run_gunicorn -c /var/www/webvirtmgr/conf/gunicorn.conf.py
directory=/var/www/webvirtmgr
autostart=true
autorestart=true
logfile=/var/log/supervisor/webvirtmgr.log
log_stderr=true
user=nginx
 
[program:webvirtmgr-console]
command=/usr/bin/python /var/www/webvirtmgr/console/webvirtmgr-console
directory=/var/www/webvirtmgr
autostart=true
autorestart=true
stdout_logfile=/var/log/supervisor/webvirtmgr-console.log
redirect_stderr=true
user=nginx
```

Restart supervisord

```
systemctl restart supervisord
```

Đến đây bạn đã cài đặt xong Webvirtmgr. Bạn mở trình duyệt và truy cập vào địa chỉ của server cài webvirtmgr. Kết quả như sau:

![](/images/img-cai-dat-webvirtmgr/2.png)

## Cài đặt trên host KVM 

Để Webvirtmgr có thể kết nối đến Host KVM và quản lý được các VM trong host KVM ta cần cấu hình một số thông tin sau trên host KVM

Trên môi trường lab nên tôi cũng tiến hành tắt firewalld 

```
systemctl stop firewalld
```

Nếu ko muốn bạn có thể mở port `16509` để webvirtmgr có thể kết nối đến

Trước tiên cần cài gói `libvirt` 

`yum install libvirt`

Thực hiện lần lượt các lệnh sau

```
sed -i 's/#listen_tls = 0/listen_tls = 0/g' /etc/libvirt/libvirtd.conf 
sed -i 's/#listen_tcp = 1/listen_tcp = 1/g' /etc/libvirt/libvirtd.conf
sed -i 's/#tcp_port = "16509"/tcp_port = "16509"/g' /etc/libvirt/libvirtd.conf
sed -i 's/#listen_addr = "192.168.0.1"/listen_addr = "0.0.0.0"/g' /etc/libvirt/libvirtd.conf
```

Lưu ý nếu bạn không muốn xác thực trong quá trình kết nối qemu+tcp ta thực hiện lệnh sau (chỉ nên dùng trong môi trường lab)

```
sed -i 's/#auth_tcp = "sasl"/auth_tcp = "none"/g' /etc/libvirt/libvirtd.conf
```

Còn nếu bạn muốn xác thực để tiến hành kết nối qemu+tcp bạn thực hiện câu lệnh sau

```
sed -i 's/#auth_tcp = "sasl"/auth_tcp = "sasl"/g' /etc/libvirt/libvirtd.conf
```

Tiếp tục thực hiện các lệnh sau:

```
sed -i 's/#user = "root"/user = "root"/g' /etc/libvirt/qemu.conf 
sed -i 's/#group = "root"/group = "root"/g' /etc/libvirt/qemu.conf
sed -i 's/#LIBVIRTD_ARGS="--listen"/LIBVIRTD_ARGS="--listen"/g' /etc/sysconfig/libvirtd
```

Restart lại libvirtd

```
systemctl restart libvirtd
```

Nếu bên trên bạn để xác thực kết nối qemu+tcp thì bạn cần thực hiện thêm một lệnh sau:

Tiến hành cài đặt gói `cyrus-sasl-md5`

```
yum install cyrus-sasl-md5
sed -i 's/mech_list: gssapi/#mech_list: gssapi/g' /etc/sasl2/libvirt.conf
sed -i 's/#sasldb_path: /etc/libvirt/passwd.db/#sasldb_path: /etc/libvirt/passwd.db/g' /etc/sasl2/libvirt.conf
```

Sau đó tiến hành restart lại libvirtd

`systemctl restart libvirtd`

Sau đó ta cần tạo user để xác thực cho kết nối qemu+tcp

Tạo user

```
saslpasswd2 -a libvirt username
```

Show các user

```
sasldblistusers2 -f /etc/libvirt/passwd.db
```

Để xóa user

```
saslpasswd2 -a libvirt -d username
```

## Sử dụng

Truy cập vào đỉa chỉ của server cài webvirt ta có giao diện như sau

![](/images/img-cai-dat-webvirtmgr/2.png)

Ta dùng tài khoản ta tạo ở bên trên để đăng nhập vào. Để kết nối đến máy KVM click và `Add connection`

![](/images/img-cai-dat-webvirtmgr/a1.png)

Chỉ ra tên để phân biệt với các kết nối khác và IP của KVM muốn kết nối. Tên đăng nhập và mật khẩu là tên user và mật khẩu bạn vừa tạo bên trên

![](/images/img-cai-dat-webvirtmgr/a2.png)

Kết nối thành công sẽ hiện lên giao diện như sau

![](/images/img-cai-dat-webvirtmgr/a3.png)

Sau đó bạn click vào kết nối đó chọn `Storages` sau đó là `New storages` để tạo vị trí lưu các file khi tạo máy ảo

![](/images/img-cai-dat-webvirtmgr/a8.png)

Chọn thư mục chứa disk của VM

![](/images/img-cai-dat-webvirtmgr/a5.png)

Chọn thư mục lưu file XML của VM

![](/images/img-cai-dat-webvirtmgr/a6.png)

Tôi lưu file ISO tại thư mục `root` nên tôi add thêm nó vào để có thể tìm thấy file ISO khi tạo VM

![](/images/img-cai-dat-webvirtmgr/a7.png)

Bạn cũng có thể thiết lập mạng cho các VM bằng cách click vào `Networks`

![](/images/img-cai-dat-webvirtmgr/a9.png)

Để tạo VM trước tiên ta cần tạo cho VM đó 1 file image. Để tạo file nào ta vào `Storage` chọn vị trí lưu file image trong các thư mục ta vừa thêm ở bên trên sau đó chọn `Add image`

![](/images/img-cai-dat-webvirtmgr/a10.png)

Chọn tên image, định dạng và dung lượng 

![](/images/img-cai-dat-webvirtmgr/a11.png)

Chọn `New instance` để tạo VM

![](/images/img-cai-dat-webvirtmgr/a12.png)

Chọn `Custom instance`

![](/images/img-cai-dat-webvirtmgr/a13.png)

Chú ý các thông số tên VM, số CPU, dung lượng RAM, HDD để chỉ ra file image của VM trỏ đến file vừa tạo, network để chọn mạng cho VM

![](/images/img-cai-dat-webvirtmgr/a14.png)

Chọn hệ điều hành cho VM bằng cách chỉ ra file ISO 

![](/images/img-cai-dat-webvirtmgr/a15.png)

Start VM

![](/images/img-cai-dat-webvirtmgr/a16.png)

Để hiển thị màn hình VM click vào biểu tượng như trên hình của VM

![](/images/img-cai-dat-webvirtmgr/a17.png)

## Tổng kết 

Đến đây ta đã tạo thành công công cụ Webvirtmgr trên CentOS 7 và biết một số bước cơ bản để tạo VM trên webvirtmgr. Để biết cách cài đặt Webvirtmgr trên Ubuntu 16 bạn tham khảo <a href="https://blog.cloud365.vn/linux/huong-dan-cai-dat-webvirtmgr-ubuntu16" target="_blank">tại đây</a>. Trong bài viết chắc chắn còn nhiều thiếu sót rất mong được sự góp ý của các bạn.

Chúc bạn thành công!

**Tài liệu tham khảo**

https://scottstechblog.com/2017/04/21/webvirtmgr/

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>