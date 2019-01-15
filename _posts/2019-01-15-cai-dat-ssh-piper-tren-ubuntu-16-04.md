# Hướng dẫn cài đặt SSH Piper trên Ubuntu 16.04

## 1. Lời mở đầu.

- Tính sau.

## 2. Mô hình triển khai và phân hoạch IP.

## 3. Các bước cài đặt.

- Cài đặt `golang`:

    ```sh
    apt install golang golang-go -y
    ```

- Tạo `GOPATH` và thiết lập biến môi trường :

    ```sh
    mkdir ~/.go
    export GOPATH=$HOME/.go
    ```

- Cài đặt ssh-piper :

    ```sh
    go get github.com/tg123/sshpiper/sshpiperd
    ```

- Tạo thư mục cấu hình host cũng như thư mục log :

    ```sh
    mkdir -p ~/.sshpiperd/config
    mkdir -p ~/.sshpiperd/log
    ```

Ở đây thì ~/.sshpiper/config thì mình sẽ dùng để cấu hình các host ssh bên trong LAN và ~/.sshpiper/log mình dùng để lưu trữ log

- Cấu trúc thư mục cấu hình host ssh piper như sau :

    ```sh
    ~/.sshpiperd/
    |
    |
    |-----config
    |       |
    |       |----sv01
    |       |      |
    |       |      |----sshpiper_upstream
    |       |
    |       |
    |       |-----sv02
    |               |
    |               |----sshpiper_upstream
    ```

Trong đó : thư mục sv01 và sv02 sẽ có file sshpiper_upstream dùng để lưu trữ thông tin cấu hình ssh đến sv01 và sv02.

- Tạo thư mục sv01 và file sshpiper_upstream :

    ```sh
    mkdir ~/.sshpiperd/config/sv01
    vi ~/.sshpiperd/config/sv01/sshpiper_upstream
    ```

- Thêm vào file ~/.sshpiperd/config/sv01/sshpiper_upstream nội dung sau :

    ```sh
    root@10.10.10.148:22
    ```

Trong đó root là user của ssh của sv01, 10.10.10.148 là địa chỉ sv01, 22 là port ssh của sv01.

- Tạo file ~/.sshpiperd/config/start.sh với nội dung như sau :

    ```sh
    #!/bin/bash

    if [ -z $GOPATH ]; then
        export GOPATH=$HOME/.go
    fi

    SSHPIPERD_BIN="$GOPATH/bin/sshpiperd"
    BASEDIR="$HOME/.sshpiperd"
    LOGFILE="$BASEDIR/log/sshpiperd.log"

    if [ ! -f $BASEDIR/sshpiperd_key ];then
        ssh-keygen -N '' -f $BASEDIR/sshpiperd_key
    fi

    for u in `find $BASEDIR/config/ -name sshpiper_upstream`; do
        chmod 400 $u
        upstream=`cat $u`

        username=`dirname $u`
        username=`basename $username`

        echo "ssh 127.0.0.1 -p 2222 -l $username # connect to $upstream"
    done

    $SSHPIPERD_BIN -i $BASEDIR/sshpiperd_key --workingdir $BASEDIR/config --log $LOGFILE
    ```

- Phân quyền cho file start :

    ```sh
    chmod +x ~/.sshpiperd/config/start.sh
    ```

- Khởi chạy file start để bắt đầu :

    ```sh
    ~/.sshpiperd/config/start.sh
    ```

- Đứng trên máy trạm bất kỳ ssh thử nghiệm với các thông số như sau :

    ```sh
    ip : 192.168.30.153
    port : 2222
    user : sv01
    ```

- Kiểm tra xem đã vào đúng máy chủ trong LAN hay chưa.

## 4. Một số lưu ý.

- Kiểm tra log của máy chủ SSH-piper : tailf /root/.sshpiperd/log/sshpiperd.log

- File ~/.sshpiperd/config/start.sh được coi như là initd của SSH-piper chúng ta có thể tạo một file trong /etc/init.d để cho dễ nhớ đường dẫn mỗi khi muốn khởi động.
