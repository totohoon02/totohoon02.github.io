# Nginx 학습 순서

## 1. 기본 개념 이해

### Nginx의 역할과 특징

Nginx(엔진엑스)는 경량 웹 서버, 리버스 프록시, 로드 밸런서 및 HTTP 캐시로 사용되는 고성능 오픈소스 소프트웨어입니다. 비동기 이벤트 기반 아키텍처를 사용하여 적은 리소스로 많은 동시 연결을 처리할 수 있습니다.

### 웹 서버와 리버스 프록시의 개념

- **웹 서버**: 클라이언트(브라우저)의 HTTP 요청을 받아 정적 파일을 제공하거나 동적 콘텐츠를 생성하는 서버
- **리버스 프록시**: 클라이언트 요청을 받아 내부 서버로 전달하고 응답을 다시 클라이언트에게 전달하는 중개자 역할

### Nginx vs Apache 비교

| 특성           | Nginx               | Apache                    |
| -------------- | ------------------- | ------------------------- |
| 아키텍처       | 이벤트 기반, 비동기 | 프로세스/스레드 기반      |
| 동시 연결 처리 | 매우 효율적         | 상대적으로 리소스 소모 큼 |
| 정적 콘텐츠    | 매우 빠름           | 좋음                      |
| 모듈 시스템    | 컴파일 시 포함      | 동적 로딩 가능            |
| 설정 난이도    | 중간                | 쉬움                      |

## 2. 설치 및 기본 설정

### 다양한 OS에 Nginx 설치하기

**Ubuntu/Debian:**

```bash
sudo apt update
sudo apt install nginx
```

**Docker:**

```bash
version: "3.8"

services:
  nginx:
    image: nginx:latest
    container_name: nginx
    restart: always
    ports:
      - "8888:80"
    volumes:
      - ./html:/usr/share/nginx/html
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./conf.d:/etc/nginx/conf.d
```

### 기본 설정 파일 구조 이해

Nginx의 주요 설정 파일 구조:

- `/etc/nginx/nginx.conf` - 메인 설정 파일
- `/etc/nginx/conf.d/` - 추가 설정 파일 디렉토리
- `/etc/nginx/sites-available/` - 사용 가능한 가상 호스트 설정
- `/etc/nginx/sites-enabled/` - 활성화된 가상 호스트 설정

### 기본 명령어 학습

```bash
# Nginx 시작
sudo systemctl start nginx

# Nginx 중지
sudo systemctl stop nginx

# Nginx 재시작
sudo systemctl restart nginx

# 설정 리로드 (서비스 중단 없이)
sudo systemctl reload nginx

# 상태 확인
sudo systemctl status nginx

# 설정 테스트
sudo nginx -t
```

## 3. 기본 설정 파일 분석

### nginx.conf 구조 이해

기본 nginx.conf 파일의 구조:

```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/conf.d/*.conf;
}
```

### 주요 지시어(directive) 학습

- `user`: Nginx 프로세스가 실행될 사용자
- `worker_processes`: 생성할 워커 프로세스 수
- `events`: 연결 처리 설정
- `http`: HTTP 서버 설정
- `server`: 가상 호스트 설정
- `location`: 특정 URI에 대한 처리 규칙

### 가상 호스트(Virtual Host) 설정

기본 가상 호스트 설정 예:

```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    root /var/www/example.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

## 4. HTTP 서버 구성

### 정적 파일 서빙 설정

정적 파일을 제공하는 기본 설정:

```nginx
server {
    listen 80;
    server_name static.example.com;

    location / {
        root /var/www/static;
        autoindex on;  # 디렉토리 인덱싱 활성화
    }

    # 특정 파일 타입에 대한 캐싱 설정
    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        root /var/www/static;
        expires 30d;  # 30일 동안 캐시
    }
}
```

### 기본 라우팅 설정

URI 기반 라우팅 예제:

```nginx
server {
    listen 80;
    server_name example.com;

    # 루트 경로
    location / {
        root /var/www/html;
        index index.html;
    }

    # /api 경로
    location /api {
        root /var/www/api;
        try_files $uri $uri/ /api/index.html;
    }

    # 정규식 매칭
    location ~ ^/users/(.+)$ {
        return 200 "User profile: $1";
    }
}
```

### MIME 타입 설정

MIME 타입 설정은 `/etc/nginx/mime.types` 파일에 정의되어 있으며, 필요시 추가할 수 있습니다:

```nginx
http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # 추가 MIME 타입 정의
    types {
        application/wasm wasm;
        font/woff2 woff2;
    }
}
```

## 5. 리버스 프록시 설정

### 프록시 기본 개념

리버스 프록시는 클라이언트 요청을 받아 내부 서버로 전달하고, 그 응답을 다시 클라이언트에게 전달합니다. 이를 통해 로드 밸런싱, 캐싱, SSL 종료 등의 기능을 제공할 수 있습니다.

### 업스트림 서버 설정

여러 백엔드 서버를 그룹화하는 업스트림 설정:

```nginx
upstream backend_servers {
    server 192.168.1.10:8080;
    server 192.168.1.11:8080;
    server 192.168.1.12:8080;
}

server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 로드 밸런싱 구성

다양한 로드 밸런싱 알고리즘 설정:

```nginx
upstream backend {
    # 라운드 로빈 (기본값)
    server 10.0.0.1:8080;
    server 10.0.0.2:8080;

    # 최소 연결 수 방식
    # least_conn;

    # IP 해시 방식 (세션 유지)
    # ip_hash;

    # 가중치 부여
    # server 10.0.0.3:8080 weight=3;
    # server 10.0.0.4:8080 weight=1;
}
```

## 6. SSL/TLS 설정

### HTTPS 구성

기본 SSL 설정:

```nginx
server {
    listen 443 ssl;
    server_name secure.example.com;

    ssl_certificate /etc/nginx/ssl/example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/example.com.key;

    # SSL 설정
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384';

    location / {
        root /var/www/secure;
        index index.html;
    }
}
```

### 인증서 설치 및 관리

Let's Encrypt를 이용한 무료 SSL 인증서 발급:

```bash
# certbot 설치
sudo apt install certbot python3-certbot-nginx

# 인증서 발급 및 Nginx 설정 자동 수정
sudo certbot --nginx -d example.com -d www.example.com

# 인증서 갱신
sudo certbot renew --dry-run
```

### SSL 최적화

SSL 성능 최적화 설정:

```nginx
server {
    listen 443 ssl http2;  # HTTP/2 활성화
    server_name secure.example.com;

    ssl_certificate /etc/nginx/ssl/example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/example.com.key;

    # SSL 세션 캐시 설정
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    ssl_session_tickets off;

    # OCSP Stapling
    ssl_stapling on;
    ssl_stapling_verify on;
    resolver 8.8.8.8 8.8.4.4 valid=300s;
    resolver_timeout 5s;

    # HSTS 설정
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
}
```

## 7. 성능 최적화

### 캐싱 설정

Nginx 캐싱 설정 예제:

```nginx
# 캐시 영역 정의
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m max_size=1g inactive=60m;

server {
    listen 80;
    server_name cache.example.com;

    location / {
        proxy_pass http://backend;
        proxy_cache my_cache;
        proxy_cache_valid 200 302 10m;
        proxy_cache_valid 404 1m;
        proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;
        proxy_cache_lock on;

        # 캐시 상태 헤더 추가
        add_header X-Cache-Status $upstream_cache_status;
    }
}
```

### Gzip 압축

콘텐츠 압축 설정:

```nginx
http {
    # Gzip 설정
    gzip on;
    gzip_comp_level 5;
    gzip_min_length 256;
    gzip_proxied any;
    gzip_vary on;
    gzip_types
        application/javascript
        application/json
        application/xml
        text/css
        text/plain
        text/xml;
}
```

### 버퍼 및 타임아웃 설정

최적의 버퍼 및 타임아웃 설정:

```nginx
http {
    # 클라이언트 버퍼 설정
    client_body_buffer_size 10K;
    client_header_buffer_size 1k;
    client_max_body_size 8m;
    large_client_header_buffers 2 1k;

    # 타임아웃 설정
    client_body_timeout 12;
    client_header_timeout 12;
    keepalive_timeout 15;
    send_timeout 10;
}
```

## 8. 보안 설정

### 기본 보안 헤더 설정

보안 강화를 위한 HTTP 헤더 설정:

```nginx
server {
    # 보안 헤더 설정
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval' https://www.google-analytics.com";
    add_header Referrer-Policy "strict-origin-when-cross-origin";

    # 서버 정보 숨기기
    server_tokens off;
}
```

### 접근 제어 설정

IP 기반 접근 제어:

```nginx
server {
    # 특정 IP만 허용
    location /admin {
        allow 192.168.1.0/24;
        allow 10.0.0.1;
        deny all;
    }

    # 기본 인증
    location /private {
        auth_basic "Restricted Access";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
}
```

### 레이트 리미팅 구현

요청 제한 설정:

```nginx
http {
    # 레이트 리미팅 영역 정의
    limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;

    server {
        # API 요청 제한
        location /api/ {
            limit_req zone=one burst=5 nodelay;
        }

        # 로그인 요청 제한
        location /login {
            limit_req zone=one burst=1 nodelay;
        }
    }
}
```

## 9. 로깅 및 모니터링

### 로그 설정 및 분석

커스텀 로그 포맷 설정:

```nginx
http {
    # 상세 로그 포맷 정의
    log_format detailed '$remote_addr - $remote_user [$time_local] '
                        '"$request" $status $body_bytes_sent '
                        '"$http_referer" "$http_user_agent" '
                        '$request_time $upstream_response_time $pipe';

    # 접근 로그 설정
    access_log /var/log/nginx/access.log detailed;

    # 에러 로그 설정
    error_log /var/log/nginx/error.log warn;

    # 특정 가상 호스트별 로그 설정
    server {
        server_name example.com;
        access_log /var/log/nginx/example.com.access.log detailed;
        error_log /var/log/nginx/example.com.error.log;
    }
}
```

### 모니터링 도구 연동

Nginx 상태 모니터링 설정:

```nginx
server {
    # Nginx 상태 페이지
    location /nginx_status {
        stub_status on;
        allow 127.0.0.1;
        deny all;
    }
}
```

### 트러블슈팅 방법

로그 분석을 위한 유용한 명령어:

```bash
# 상태 코드별 요청 수 확인
awk '{print $9}' /var/log/nginx/access.log | sort | uniq -c | sort -rn

# 가장 많이 요청된 URL 확인
awk '{print $7}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -n 10

# 느린 요청 찾기 (1초 이상)
awk '$NF > 1 {print $7, $NF}' /var/log/nginx/access.log | sort -k2 -nr | head -n 10
```

## 10. 고급 기능

### 웹소켓 프록시

웹소켓 연결 프록시 설정:

```nginx
server {
    listen 80;
    server_name websocket.example.com;

    location /ws {
        proxy_pass http://websocket_backend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_read_timeout 3600s;  # 웹소켓 연결 유지 시간
    }
}
```

### FastCGI 설정

PHP-FPM과 연동하는 FastCGI 설정:

```nginx
server {
    listen 80;
    server_name php.example.com;
    root /var/www/html;

    location / {
        index index.php index.html;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

### 스트리밍 미디어 서비스

비디오 스트리밍 설정:

```nginx
server {
    listen 80;
    server_name stream.example.com;

    # HLS 스트리밍
    location /hls {
        root /var/www/media;
        add_header Cache-Control no-cache;
    }

    # MP4 스트리밍
    location ~ \.mp4$ {
        root /var/www/media;
        mp4;
        mp4_buffer_size 1m;
        mp4_max_buffer_size 5m;
    }
}
```

## 11. 실전 배포 사례

### 다양한 애플리케이션 서버와 연동

Node.js 애플리케이션 연동:

```nginx
server {
    listen 80;
    server_name nodejs.example.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Django 애플리케이션 연동:

```nginx
server {
    listen 80;
    server_name django.example.com;

    location /static/ {
        alias /var/www/django/static/;
    }

    location /media/ {
        alias /var/www/django/media/;
    }

    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### 마이크로서비스 아키텍처에서의 활용

API 게이트웨이 구성:

```nginx
server {
    listen 80;
    server_name api.example.com;

    # 사용자 서비스
    location /users {
        proxy_pass http://user-service:8001;
    }

    # 상품 서비스
    location /products {
        proxy_pass http://product-service:8002;
    }

    # 주문 서비스
    location /orders {
        proxy_pass http://order-service:8003;
    }

    # 공통 헤더 설정
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

### 컨테이너 환경에서의 Nginx 구성

Docker Compose를 이용한 Nginx 설정:

```yaml
# docker-compose.yml
version: "3"
services:
  nginx:
    image: nginx:latest
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./html:/usr/share/nginx/html
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - app
    networks:
      - app-network

  app:
    image: my-app:latest
    expose:
      - "8080"
    networks:
      - app-network

networks:
  app-network:
    driver: bridge
```

## 12. 트러블슈팅 및 디버깅

### 일반적인 오류 해결

주요 Nginx 오류 코드와 해결 방법:

| 오류 코드           | 설명                         | 해결 방법                             |
| ------------------- | ---------------------------- | ------------------------------------- |
| 403 Forbidden       | 파일 접근 권한 문제          | 디렉토리 권한 확인, SELinux 설정 확인 |
| 502 Bad Gateway     | 업스트림 서버 연결 실패      | 백엔드 서버 상태 확인, 포트 확인      |
| 504 Gateway Timeout | 업스트림 서버 응답 시간 초과 | 타임아웃 설정 조정, 백엔드 성능 확인  |

### 성능 병목 현상 분석

성능 분석 도구 사용:

```bash
# 연결 상태 확인
netstat -an | grep :80 | wc -l

# CPU/메모리 사용량 확인
top -p $(pgrep -d',' nginx)

# 파일 디스크립터 사용량 확인
lsof -p $(pgrep -d',' nginx) | wc -l
```

### 로그 분석을 통한 문제 해결

로그 분석 스크립트 예제:

```bash
#!/bin/bash
# 최근 10분간 오류 로그 확인
echo "=== 최근 오류 로그 ==="
grep "error" /var/log/nginx/error.log | tail -n 20

# 상태 코드별 통계
echo "=== 상태 코드 통계 ==="
awk '{print $9}' /var/log/nginx/access.log | sort | uniq -c | sort -rn

# 느린 요청 확인
echo "=== 느린 요청 (1초 이상) ==="
awk '$NF > 1 {print $7, $NF}' /var/log/nginx/access.log | sort -k2 -nr | head -n 10
```
