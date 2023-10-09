<img src="https://share.adam.ac/21/logo-g3hWemROTemCYbWlmL.svg" width="250" />

## Table of contents
- [Hostname](#hostname)
- [Docker](#docker)
- [Postal](#postal)
  - [Chuẩn bị](#chuẩn-bị)
  - [Cấu hình](#cấu-hình)
- [References](#references)

### Hostname
- Set Hostname
```
sudo hostnamectl set-hostname postal.hadt.space
```
- Lệnh kiểm tra
```
hostname
```
### Docker 
- Cài đặt một số gói cho phép sử dụng HTTPS.
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
```
- Thêm khóa GPG của kho lưu trữ Docker.
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
- Thêm kho lưu trữ Docker của Ubuntu vào các apt sources.
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- Cập nhật packages và thiết lập để cài đặt Docker từ kho lưu trữ chính thức.
```
sudo apt update
```
```
sudo apt-cache policy docker-ce
```
- Cài đặt Docker
```
sudo apt install docker-ce -y
```
- Kiểm tra trạng thái của Docker
```
sudo systemctl status docker
```
- Xem các thông tin về Docker
```
docker info
```

### Postal 
#### Chuẩn bị 
- Cài các tool cần thiết
```
sudo apt install git curl jq
```
- Download file cài 
```
git clone https://github.com/postalserver/install/ /opt/postal/install
```
- Tạo link thư mục
```
sudo ln -s /opt/postal/install/bin/postal /usr/bin/postal
```


- Chạy Mariadb trên docker 
   + Trước khi chạy cần kiểm tra xem có phần mềm nào chiếm port 3306 hay không
    ```
    lsof -i:3306
    ```
   + Nếu có thì sẽ dừng service đang chiếm port 3306 lại
    ```
    systemctl stop mysql.service mysqld.service

    ```
   + Đảm bảo `lsof -i:3306` không còn service nào thì tiếp tục
   + Lệnh chạy mariadb trên docker
    ```
    docker run -d \
       --name postal-mariadb \
       -p 127.0.0.1:3306:3306 \
       --restart always \
       -e MARIADB_DATABASE=postal \
       -e MARIADB_ROOT_PASSWORD=postal \
       mariadb
    ```

- Chạy RabbitMQ trên docker
```
docker run -d \
   --name postal-rabbitmq \
   -p 127.0.0.1:5672:5672 \
   --restart always \
   -e RABBITMQ_DEFAULT_USER=postal \
   -e RABBITMQ_DEFAULT_PASS=postal \
   -e RABBITMQ_DEFAULT_VHOST=postal \
   rabbitmq:3.8
```
#### Cấu hình
- Tạo file cấu hình
    + Chạy lệnh
    ```
    postal bootstrap postal.hadt.space
    ```

    - Lệnh này sinh ra file cấu hình `postal.yml` trong thư mục `/opt/postal/config` đây là file cấu hình chính của postal
- Sừa file cấu hình
    + Mở file
    ```
    vim /opt/postal/config/postal.yml
    ```
    + Replace `postal.example.com` bằng `postal.hadt.space` 
    + Trong phần DNS (dòng 46)
        + Sửa mx record thành 
        ```
        mx_records:
            - mail.hadt.space
        ```
    + Trong phần SMTP (dòng 67)
        + Sửa from_address thành 
        ```
        from_address: postal@hadt.space
        ```

- Khởi tạo cơ sở dữ liệu
```
postal initialize
```
- Tạo user admin
```
postal make-user
```
- Khởi chạy postal 
```
postal start
```
- Cài đặt Caddy(để xử lí SSL và lưu lượng truy cập web)
```
docker run -d \
   --name postal-caddy \
   --restart always \
   --network host \
   -v /opt/postal/config/Caddyfile:/etc/caddy/Caddyfile \
   -v /opt/postal/caddy-data:/data \
   caddy
```

- Thêm bản ghi DNS 

| Tên bản ghi   | Loại bản ghi | Giá trị bản ghi                                                 | Ưu tiên |
|---------------|--------------|-----------------------------------------------------------------|---------|
| postal        | A            | 45.124.93.82                                                    |         |
| track.postal  | A            | 45.124.93.82                                                    |         |
| mail          | A            | 45.124.93.82                                                    |         |
| rp.postal     | A            | 45.124.93.82                                                    |         |
| psrp          | CNAME        | rp.postal.hadt.space                                            |         |
| routes.postal | MX           | mail.hadt.space                                                 | 10      |
| rp            | MX           | mail.hadt.space                                                 | 10      |
| @             | MX           | mail.hadt.space                                                 | 10      |
| rp.postal     | MX           | postal.hadt.space                                               | 10      |
| routes.postal | MX           | postal.hadt.space                                               | 10      |
| _dmarc        | TXT          | v=DMARC1; p=reject; sp=reject; rua=mailto:postmaster@hadt.space |         |
| spf.postal    | TXT          | v=spf1 ip4:45.124.93.82 ~all                                    |         |
| rp.postal     | TXT          | v=spf1 a mx include:spf.mail.hadt.space ~all                    |         |
| @             | TXT          | v=spf1 a mx include:spf.postal.hadt.space ~all                  |         |

Dashboard: https://postal.hadt.space

### References 
* [Document Postal](https://docs.postalserver.io/)
* [HOW TO INSTALL & CONFIGURE POSTAL MAIL SERVER ON UBUNTU](https://technicalsahil.com/how-to-install-configure-postal-mail-server-on-ubuntu/)
