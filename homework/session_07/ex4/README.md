# Bài 4 — Cấu hình Reverse Proxy Nginx cho ứng dụng Spring Boot

## Cấu trúc nộp
- `spring-proxy.conf` — Server Block Nginx
- `index.html` — trang tĩnh thông tin học viên

## Các bước triển khai trên VPS

```bash
sudo mkdir -p /var/www/html
sudo cp index.html /var/www/html/
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html

sudo cp spring-proxy.conf /etc/nginx/sites-available/spring-proxy.conf
sudo ln -s /etc/nginx/sites-available/spring-proxy.conf /etc/nginx/sites-enabled/
sudo rm -f /etc/nginx/sites-enabled/default

sudo nginx -t
sudo systemctl reload nginx
```

## Kết quả `sudo nginx -t`

```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

## Kết quả `curl -I http://localhost/`

```text
HTTP/1.1 200 OK
Server: nginx
Content-Type: text/html
```

## Định tuyến

| Path | Xử lý |
|------|--------|
| `/` | Static: `/var/www/html/` |
| `/api/` | Reverse proxy → `http://127.0.0.1:8082/` |

Header chuyển tiếp: `Host`, `X-Real-IP`, `X-Forwarded-For`, `X-Forwarded-Proto`.

Lưu ý: `/api/health` trả về từ Spring Boot khi app đang chạy cổng 8082; nếu app chưa chạy sẽ 502.
