---
title: Hướng dẫn sử dụng Fail2ban trên CentOS
categories:
  - Linux
description: Hướng dẫn sử dụng Fail2ban trên CentOS
author: canhdx
tags: [Beginer, Linux, Other]
type: Document
---

## Bối cảnh sử dụng 

Khi sử dụng các Server VPS Linux chúng ta thường sử dụng SSH remote thao tác với Server. 

Mặc dù SSH là một giao thức đã được bảo mật hoạt động ở lớp trên trong mô hình TCP/IP (thường) sử dụng port 22 nhưng cũng tồn tại những rủi ro nhất định cho trong quá trình vận hành lý do là vì ssh phơi ra phía ngoài internet. Việc bị dò mật khẩu là một trong những cách tấn công phổ biến nhất.

<p align="center">
<img src="/images/img-fail2ban/brute-force.jpg">
</p>

Các biện pháp Secure SSH thường được sử dụng nhằm tránh, hạn chế bị tấn công `Brute force` (Dò và thử đến khi đăng nhập được)
- Username và Mật khẩu mạnh 
- Cấu hình timeout cho 1 session SSH, cấu hình  `/etc/ssh/sshd_config`
    ```sh 
    ClientAliveInterval 360
    ClientAliveCountMax 0
    ```
- Không cho phép password trống, cấu hình `/etc/ssh/sshd_config`
    ```sh 
    PermitEmptyPasswords no
    ```
- Giới hạn các user được phép SSH, cấu hình `/etc/ssh/sshd_config`
    ```sh 
    AllowUsers user1 user2
    ```
    > Thường để bảo mật chúng ta để các user được phép SSH là user thường sau khi login vào được server chúng ta mới thực hiện chuyển sang user khác để thao tác
- Không cho phép `root` login, cấu hình `/etc/ssh/sshd_config`
    ```sh 
    PermitRootLogin no
    ```
- Sử dụng mặc đinh Protocol2 cho SSH, cấu hình `/etc/ssh/sshd_config`
    ```sh 
    Protocol 2
    ```
- Sử dụng port khác port 22, cấu hình `/etc/ssh/sshd_config`
    ```sh 
    Port 2345
    ```
- Cho phép ssh từ một số IP, nhất đinh, cấu hình iptables
    ```sh 
    iptables -A INPUT -p tcp -s YourIP --dport 2345 -j ACCEPT

    iptables -A INPUT -p tcp --dport 2345 -m state --state NEW -m recent --set --name ssh --rsource

    iptables -A INPUT -p tcp --dport 2345 -m state --state NEW -m recent ! --rcheck --seconds 90 --hitcount 3 --name ssh --rsource -j ACCEPT
    ```
- Bật sử dụng xác thực 2 lớp <a href="https://www.linux.com/blog/securing-ssh-two-factor-authentication-using-google-authenticator" target="_blank">link</a>

- Sử dụng key để ssh thay cho password 
    + Thao tác trên máy client 
        <!-- -->
        ```sh 
        ssh-keygen -t rsa
        ```
    + Copy key qua Server
        ```sh 
        ssh-copy-id username@server 
        ```
- Và một vài secure khác khi được kết hợp trong hệ thống như VPN, Fail2ban 

Trường hợp vẫn bị quét và chúng ta muốn block hẳn IP của Client đang ssh liên tục vào hệ thống chúng ta sẽ sử dụng fail2ban. Fail2ban sẽ theo dõi log file để phát hiện những địa chỉ IP đăng nhập sai password SSH nhiều lần. Sau đó, Fail2Ban sử dụng iptable firewall rules để block.

## Chuẩn bị 

1 Server Linux cài đặt Fail2ban 
- OS: CentOS7
- RAM: >= 1 GB
- Disk: >= 10 GB
- CPU: >= 1 Core 3
- Update và cài đặt openssh-server
- IP: 10.10.10.244/24

1 máy là có cài đặt SSH Client để  tiến hành dò ssh server phía trên 
- Có cài đặt SSH Client
- IP: 10.10.10.217/24

<p align="center">
<img src="/images/img-fail2ban/topo.png">
</p>

## Theo dõi ssh login failed trên Server

- Trên máy Client.217 thực hiện ssh nhiều lần sai password 

- Trên máy Server.214 thực hiện kiểm tra 
    ```sh 
    cat /var/log/secure | grep 'Failed password' | sort | uniq -c
    ```
    > Kết quả 
    ```sh 
    [root@server ~]# cat /var/log/secure | grep 'Failed password' | sort | uniq -c
        1 Feb 14 14:39:49 centos7 sshd[4714]: Failed password for root from 10.10.10.217 port 43460 ssh2
        1 Feb 14 14:39:54 centos7 sshd[4714]: Failed password for root from 10.10.10.217 port 43460 ssh2
        1 Feb 14 14:40:12 centos7 sshd[4714]: Failed password for root from 10.10.10.217 port 43460 ssh2
    [root@server ~]# 
    ```

- Trong quá trình này Client cứ ssh liên tục cho đến khi đoán được password đúng và `BÙM!!` server của bạn đã nằm trong tay người khác

## Cài đặt và cấu hình 

- Cài đặt 
    ```sh 
    yum install epel-release -y 
    yum install fail2ban -y 
    ```

- Cấu hình fail2ban
    ```sh 
    cat << EOF >> /etc/fail2ban/jail.conf
    [DEFAULT]
    ignoreip = 127.0.0.1
    bantime = 600
    findtime = 600
    maxretry = 3
    EOF
    ```
    Trong đó
    - ignoreip: không block những địa chỉ này
    - bantime: khoảng thời gian block mặc định (giây)
    - findtime: khoảng thời gian (giây) một IP phải login thành công
    - maxretry: số lần login false tối đa

- Cấu hình fail2ban cho ssh 
    ```sh 
    cat << EOF >> /etc/fail2ban/jail.local
    [sshd]
    enabled  = true
    filter   = sshd
    action   = iptables[name=SSH, port=ssh, protocol=tcp]
    logpath  = /var/log/secure
    maxretry = 5
    bantime = 3600
    EOF
    ```
    Trong đó
    - enabled: kích hoạt bảo vệ
    - filter: giữ mặc định để sử dụng file cấu hình /etc/fail2ban/filter.d/sshd.conf
    - action: fail2ban sẽ ban địa chỉ IP nếu match filter /etc/fail2ban/action.d/iptables.conf
    - logpath: đường dẫn file log fail2ban 
    - maxretry: số lần login false tối đa
    - bantime: thời gian ban IP 3600 giây = 1 giờ

- Start fail2ban 
    ```sh 
    systemctl enable fail2ban
    systemctl start fail2ban
    ```

- Kiểm tra rulues của fail2ban trên iptables
    ```sh 
    iptables -L
    ```
    > Output 
    ```sh 
    Chain INPUT (policy ACCEPT)
    target     prot opt source               destination         
    f2b-SSH    tcp  --  anywhere             anywhere             tcp dpt:ssh

    Chain FORWARD (policy ACCEPT)
    target     prot opt source               destination         

    Chain OUTPUT (policy ACCEPT)
    target     prot opt source               destination         

    Chain f2b-SSH (1 references)
    target     prot opt source               destination         
    RETURN     all  --  anywhere             anywhere   
    ```


## Thực hiện kiểm tra sau khi cài đặt fail2ban 

- Trên máy Client thực hiện ssh nhiều lần sai password lại 
    > Kết quả: Sau 5 lần ssh thất bại do sai password máy Client sẽ bị block trong 1 tiếng 
    ```sh 
    root@client:~# ssh root@10.10.10.244
    ssh: connect to host 10.10.10.244 port 22: Connection refused
    root@client:~# 
    ```

- Kiểm tra trên Server
    ```sh 
    cat /var/log/secure | grep 'Failed password' | sort | uniq -c
    ```

- Để  kiểm tra các IP đã bị fail2ban cấm ssh chúng ta thực hiện 
    ```sh 
    fail2ban-client status sshd
    ```
    > Output trả về 
    ```sh 
    [root@server ~]# fail2ban-client status sshd
    Status for the jail: sshd
    |- Filter
    |  |- Currently failed:	1
    |  |- Total failed:	6
    |  `- Journal matches:	_SYSTEMD_UNIT=sshd.service + _COMM=sshd
    `- Actions
    |- Currently banned:	1
    |- Total banned:	1
    `- Banned IP list:	10.10.10.217
    [root@server ~]# 
    ```

- Để xóa IP đã bị cấm ra khỏi danh sách 
    ```sh 
    fail2ban-client set sshd unbanip 10.10.10.217
    ```

- Lúc này đứng trên Client có thể thực hiện ssh lại bình thường
    ```sh 
    root@client:~# ssh root@10.10.10.244
    root@10.10.10.244's password: 
    ```

## Kết luận

Fail2ban mà chúng tôi giới thiệu phía trên chỉ là một trong những giải pháp hiệu quả trong việc ngăn chặn bute-force. Việc phối hợp nhiều biện pháp khác nhau nhằm bảo vệ SSH là vô cùng cần thiết.

### Tài liệu tham khảo
[http://www.fail2ban.org/wiki/index.php/Main_Page](http://www.fail2ban.org/wiki/index.php/Main_Page){:target="_blank"}

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>

