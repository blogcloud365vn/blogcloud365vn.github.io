## EasyEngine là gì?

EasyEngine là một *UNIX script dành cho hệ điều hành Ubuntu/Debian giúp tự động cài đặt một webserver sử dụng NGINX và PHP-FPM hoàn chỉnh phục vụ riêng cho WordPress. Điều này có nghĩa là sẽ tự tối ưu hóa cho NGINX để làm việc với WordPress tốt nhất. 

Các chức năng của NGINX: 
+ Tự cài đặt NGINX bản mới nhất.
+ Tự thiết lập PHP-FPM cho NGINX.
+ Có hỗ trợ HHVM.
+ Hỗ trợ Redis Cache.
+ Hỗ trợ SSL miễn phí từ Let’s Encrypt.
+ Hỗ trợ tự cài website WordPress mới nhanh chóng.
+ Tự thiết lập các cấu hình dành cho WordPress bao gồm cấu hình cho WP Super Cache, W3 Total Cache, Redis Cache và ngx_fastcgi_cache
+ Hỗ trợ Opcache, Memcached.
+ Cài sẵn phpMyAdmin và bảo mật.
+ Cài sẵn Postfix để gửi mail.
+ Có sẵn WP-CLI để quản trị WordPress với dòng lệnh. Cái này khá quan trọng cho người dùng WordPress.
+ Cấu hình NGINX cho WordPress Multisite.
EasyEngine là sự lựa chọn rất tối ưu nếu sử dụng chỉ một vài website cá nhân trên máy chủ. 
Lưu ý: 
+ NGINX không sử dụng .htacces
+ Sử dụng EasyEngine 100% qua dòng lệnh, đây là script chứ không phải là control panel.

## Thêm domain và cài WordPress tự động
Để thêm domain vào và tự động cài website WordPress mới, có những dòng lệnh như sau:

```sh
ee site create domain.com --wp
ee site create domain.com --w3tc
ee site create domain.com --wpfc
ee site create domain.com --wpsc
ee site create domain.com --wpredis
```

Trong đó, sự khác nhau là giữa các tham số là:

- `--wp`: Tự cài đặt một website WordPress đơn giản.
- `--w3tc`: Tự cài đặt một website WordPress chạy với plugin W3 Total Cache.
- `--wpfc`: Tự cài đặt một website WordPress chạy với NGINX FastCGI Cache.
- `--wpsc`: Tự cài đặt một website WordPress chạy với plugin WP Super Cache.
- `--wpredis`: Tự cài đặt một website WordPress sử dụng Redis làm Page Cache và Object Cache.

Trong đó, khuyến khích sử dụng `--wpfc` hoặc `--wpredis` vì nhẹ. Nếu phân vân trong sự lựa chọn thì có thể chọn cấu hình `--w3tc` để dễ sử dụng.

### TỰ CẤU HÌNH MẬT KHẨU VÀ USERNAME

Mặc định nếu thêm domain vào và cài đặt thì username và mật khẩu của website sẽ tạo ra ngẫu nhiên. Ngoài ra có thể thêm tham số `--user` và `--pass` vào để thiết lập lại. Ví dụ:

```sh
ee site create domain.com --wpredis --user=admin --pass=123456
```

### THÊM WEBSITE SỬ DỤNG WORDPRESS MULTISITE

Thay vì sử dụng WordPress thông thường, nếu muốn tự thêm domain vào và sử dụng với WordPress Multisite trên NGINX thì có thể thêm tham số `--wpsubdir` hoặc `--wpsubdom` vào, trong đó:

- `--wpsubdir`: WordPress Multisite sử dụng tên miền dạng thư mục như www.domain.com/site/
- `--wpsubdom`: WordPress Multisite sử dụng tên miền dạng subdomain như www.site.domain.com.

Ví dụ:

```sh
ee site create domain.com --wpredis --wpsubdir
```

### THAY ĐỔI CẤU HÌNH WEBSITE

Giả sử đang sử dụng cấu hình `--wpredis` và muốn đổi thành cấu hình `--w3tc` thì có thể sử dụng lệnh ee site update như sau.

```sh
	ee site update domain.com --w3tc
```

### Lệnh xóa domain

Để xoá một website trên máy chủ, cũng như xoá toàn bộ dữ liệu thì có thể sử dụng lệnh sau: 

```sh
ee site delete domain.com
```

## Các file cấu hình cơ bản

### Cấu hình NGINX:
- /etc/nginx/ – Tất cả các file cấu hình liên quan tới NGINX đều lưu ở đây.
- /etc/nginx/nginx.conf – File cấu hình chính của NGINX.
- /etc/nginx/sites-available/ – File cấu hình của các domain đã thêm vào máy chủ. Nếu cần sửa gì ở từng domain thì nên sửa trong đây.
- /etc/nginx/sites-enables/ – Thư mục chứa các symbolic link dẫn đến file cấu hình của domain tương ứng, chỉ có trong đây nếu domain đó đang kích hoạt. Không cần sửa/xem.
- /etc/nginx/common/ – Thư mục chứa các cấu hình thêm vào NGINX như hhvm, W3 Total Cache, WP Super Cache, Pagespeed…
Log
- /var/log/nginx/ – Thư mục chứa toàn bộ các file log của NGINX.
- /var/log/nginx/example.com.access.log – File access log của domain example.com.
- /var/log/nginx/example.com.error.log – File error log của domain example.com.

## Cấu hình PHP
- /etc/php5/ – Tất cả các file cấu hình của PHP nằm trong đây.
- /etc/php5/fpm/php.ini – File cấu hình chính của PHP.
- /etc/php5/fpm/php-fpm.conf – Các cài đặt của PHP-FPM.
- /etc/php5/fpm/conf.d/www.conf – File cấu hình PHP-FPM riêng lẻ, có thể tạo ra nhiều file với tên khác nhau nếu có nhu cầu.

**Log:**

- /var/log/php5-fpm/ – Tất cả các log liên quan đến PHP-FPM đều nằm ở đây.
- /var/log/php5-fpm/slow.log – Xem log các script thực thi chậm.
- /var/log/php5-fpm/php.log – Log liên quan đến PHP.

### Cấu hình MySQL
- /etc/mysql/my.cnf – Đây là file cấu hình chính dành cho MySQL.
- /root/.my.cnf – File này chứa user & pass root của MySQL.

**Log:**
- /var/log/mysql/mysql.log – Các log chung liên quan đến MySQL.
- /var/log/mysql/mysql-slow.log – Log ghi lại các query thực thi lâu đến MySQL

## Cấu trúc thư mục website
- /var/www – Tất cả các thư mục của từng domain sẽ được lưu trong đây.
- /var/www/example.com – Tất cả dữ liệu liên quan đến domain example.com đều lưu trong đây. Thư mục này cũng chứa file wp-config.php của WordPress.
- /var/www/example.com/htdocs – Thư mục gốc chứa dữ liệu website mà sẽ hiển thị ra ngoài, đây chính là thư mục public_html hay gặp.
- /var/www/example.com/logs – Thư mục chứa log liên quan đến domain example.com

