---
title: "Hướng dẫn sử dụng Jekyll - Part 2: Cài đặt Jekyll trên CentOS 7"
categories:
  - Other
description: Tài liệu hướng dẫn sử dụng Jekyll, Phần 1 - Cài đặt Jekyll trên CentOS 7
author: huytm
tags: [Jekyll, CentOS]
type: Document
---

Ở bài viết trước của mình, mình có nói đến việc cài đặt một con **Local jekyll** vậy nên ở bài này mình sẽ viết lại các bước cụ thể để có thể cài đặt và sử dụng được nó. Nếu ai chưa biết về Jekyll hay Github pages là gì thì quay lại đọc bài viết trước của mình tại [đây](https://blog.cloud365.vn/other/huong-dan-su-dung-Jekyll-Part1-Github-pages/){:target="_blank"} nhé. 

Sẽ có nhiều người tự hỏi. __Ủa, sao có Github pages rồi còn phải chơi **Local jekyll** để làm gì?__ Với câu hỏi này mình sẽ trả lời như sau: 
- Thứ nhất: Theo trang chủ của Github khuyến cáo thì việc cài đặt __Local Jekyll__ là để test và troubleshoot khi Blog của bạn có thay đổi về mã nguồn hoặc các các lib liên quan.
- Thứ hai: Đối với mình việc cài đặt này là để mày mò, để bổ sung thêm kiến thức cho bản thân. Ngoài ra làm cái gì mà mình có thể kiểm soát được nó hoàn toàn thì vẫn tốt hơn đúng không?
- Thứ ba: Để viết tut phục vụ ai có nhu cầu sử dụng jekyll.

> Có thể bạn biết thừa: Blog này được chạy bằng **Local jekyll** đó =))

## 1. Chuẩn bị

Chuẩn bị một [VPS](https://cloud365.vn/){:target="_blank"} có cấu hình tối thiểu như sau:

![](/images/img-jekyll/image1.png)

## 2. Stop firewall và SELinux

Vì bài viết này là hướng dẫn cơ bản nên mình sẽ chạy các command phía dưới với quyền `root` và setup server của mình chạy trong môi trường lý tưởng. Nếu các bạn chạy trên __production__, để đảm bảo an toàn thì nhớ enable và set rule cho firewall nhé.

```
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld
useradd jekyll

reboot
```

## 3. Update OS và cài đặt một số gói cần thiết

```
yum -y update
yum install vim wget unzip git -y
```

## 4. Cài đặt Ruby

__a. Cài đặt rvm__
```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
\curl -sSL https://get.rvm.io | bash -s stable
```
__b. Cài đặt ruby__

```
source /etc/profile.d/rvm.sh
rvm install ruby-2.5
ruby -v
gem -v
```

__c. Giải thích một chút:__

Vì Jekyll được viết bằng __ruby__ và yêu cầu ruby từ __version 2.2.5__ trở lên nên mình sẽ cài ruby version mới nhất tại thời điểm viết bài viết này là __2.5__
- **rvm** là Ruby Version Manager, sẽ hỗ trợ mình kiểm soát và cài đặt version ruby mong muốn. Ngoài ra rvm cũng dùng để **switch** các phiên bản ruby một cách dễ dàng chỉ bằng một câu lệnh.
- **gem** như bài viết trước mình có nói, __gem__ là một hệ thống quản lý thư viện (libs-packages-managers) để quản lý các thư viện, các plugin được viết bằng Ruby. Nó cũng tương đương với npm của javascript, mvn của java, hay composer của php.

## 5. Cài đặt Jekyll

```
gem install jekyll -v 3.7.4
gem install bundler
jekyll -v
```

__bundler__ dùng để quản lý, install, uninstall.. các gem. Các gem là đây là các lib phục vụ cho jekyll 


## 6. Deploy Jekyll

Như đã nói, các bạn có thể tự build một blog cho riêng mình hoặc tải một theme free có sẵn trên mạng để deploy một blog cho riêng mình.

Ở đây mình chọn blog của [__cloud365vn__](https://cloud365.vn){:target="_blank"} và Github của cloud365 ở [đây](https://github.com/blogcloud365vn/blogcloud365vn.github.io){:target="_blank"}. Các bạn cũng có thể contribute với __cloud365__ để chia sẻ kiến thức của mình cho những người khác nữa nhé :D.  

Vì loài người rất đáng sợ, nên mình sẽ chạy blog của mình dưới quyền của `jekyll` !

**a. Clone repo blog của cloud365**
```
cd /opt/
git clone https://github.com/blogcloud365vn/blogcloud365vn.github.io.git
mv blogcloud365vn.github.io myblog
chown -R jekyll:jekyll /opt/myblog
```

__b. Sử dụng `bundler` để install các gem lib__

Chú ý xóa file `Gemfile.lock` trước khi install

```
cd /opt/myblog/
rm -f Gemfile.lock
bundle install
```


__c. Quản lý tiến trình jekyll với `systemctl`__

Tìm đường dẫn gốc của `jekyll`

```
which jekyll
# /usr/local/rvm/gems/ruby-2.5.3/bin/jekyll
```

Chú ý. Với đường dẫn gốc trên khi chạy jekyll trong systemctl sẽ bị sai vậy nên đường dẫn gốc lúc nào phải là 

`/usr/local/rvm/gems/ruby-2.5.3/wrappers/jekyll`

Tạo file `/etc/systemd/system/jekyll.service` với nội dung sau:

```
[Unit]
Description=Jekyll service
After=syslog.target
After=network.target
[Service]
User=jekyll
Type=simple
WorkingDirectory=/opt/myblog
ExecStart=/usr/local/rvm/gems/ruby-2.5.3/wrappers/jekyll serve --host [YOUR_IP] --port 8080 --watch "/opt/myblog"
ExecStop=/usr/bin/pkill -u jekyll
Restart=always
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=jekyll
[Install]
WantedBy=multi-user.target
```

Khởi động jekyll

```
systemctl daemon-reload
systemctl start jekyll
```

## 7. Truy cập vào jekyll

Truy cập vào jekyll theo địa chỉ  `http://YOUR_IP:8080`

![Image done](https://github.com/huytm/How-to-Jekyll/blob/master/images/blogcloud365-jekyll.png?raw=true)

## Tổng kết.

Vậy là mình đã kết thúc series 2 bài với Jekyll và Github page. Hy vọng loạt bài này của mình sẽ giúp ích được gì đó cho các bạn. Nếu có thắc mắc gì các bạn có thể liên hệ với cloud365 để được giải đáp nhé.

Chúc các bạn thành công và cảm ơn các bạn đã theo dõi loạt bài viết này. Hẹn gặp lại trong các bài viết tiếp theo của mình :D

>"if you have knowledge let others light their candles in it"

---

### Tài liệu tham khảo

[https://jekyllrb.com/docs/installation/](https://jekyllrb.com/docs/installation/){:target="_blank"}

[https://help.github.com/](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/){:target="_blank"}

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
