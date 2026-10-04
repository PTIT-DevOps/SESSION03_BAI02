# BT02 - Cấu hình trang lỗi tùy chỉnh (Custom Error Page 404)

## 1. Tạo file 404.html trong web root

```bash
devops@0310263186:~$ sudo mkdir -p /var/www/my-web/html
devops@0310263186:~$ sudo nano /var/www/my-web/html/404.html
devops@0310263186:~$ cat /var/www/my-web/html/404.html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>404 Not Found</title>
</head>
<body>
    <h1>404 - Không tìm thấy trang</h1>
    <p>Đường dẫn bạn truy cập không tồn tại trên hệ thống.</p>
</body>
</html>
```

## 2. Cấu hình Server Block Nginx

```bash
devops@0310263186:~$ sudo nano /etc/nginx/sites-available/my-web.conf
devops@0310263186:~$ cat /etc/nginx/sites-available/my-web.conf
server {
    listen 80;
    listen [::]:80;
    server_name _;

    root /var/www/my-web/html;
    index index.html;

    error_page 404 /404.html;

    location = /404.html {
        root /var/www/my-web/html;
        internal;
    }

    location / {
        try_files $uri $uri/ =404;
    }
}
```

## 3. Kích hoạt và kiểm tra cú pháp

```bash
devops@0310263186:~$ sudo ln -s /etc/nginx/sites-available/my-web.conf /etc/nginx/sites-enabled/my-web.conf
devops@0310263186:~$ sudo rm -f /etc/nginx/sites-enabled/default
devops@0310263186:~$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
devops@0310263186:~$ sudo systemctl reload nginx
```

## 4. Kiểm tra kết quả

Truy cập một đường dẫn không tồn tại:

```bash
thinh@local:~$ curl -i http://160.30.160.103/invalid-path-demo
HTTP/1.1 404 Not Found
Server: nginx/1.24.0 (Ubuntu)
Date: Sun, 04 Oct 2026 14:48:20 GMT
Content-Type: text/html
Content-Length: 236

<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>404 Not Found</title>
</head>
<body>
    <h1>404 - Không tìm thấy trang</h1>
    <p>Đường dẫn bạn truy cập không tồn tại trên hệ thống.</p>
</body>
</html>
```

Truy cập trực tiếp file lỗi:

```bash
thinh@local:~$ curl -I http://160.30.160.103/404.html
HTTP/1.1 404 Not Found
Server: nginx/1.24.0 (Ubuntu)
Date: Sun, 04 Oct 2026 14:48:31 GMT
Content-Type: text/html
Content-Length: 236
```
