# ================================
# HTTPS Server Block
# ================================
server {
    listen 443 ssl http2;
    server_name <domain>;

    # ======================
    # TLS Certificates (Let's Encrypt)
    # ======================
    ssl_certificate     /etc/nginx/ssl/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers HIGH:!aNULL:!MD5;

    # ======================
    # Logging
    # ======================
    access_log /var/log/nginx/access.log;
    error_log  /var/log/nginx/error.log;

    # ======================
    # WebSocket Proxy (Port 8888)
    # ======================
    location /ws/ {
        proxy_pass http://localhost:8888;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }

    # ======================
    # API/Backend Proxy (Port 8000)
    # ======================
    location /api/ {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    # ======================
    # Static Files (HTML, JS, CSS, images, etc.)
    # ======================
    location /static/ {
        root /var/www/<domain>/;
        try_files $uri $uri/ =404;

        # Cache static files
        expires 30d;
        access_log off;
    }

    # ======================
    # Main App Entry (Single Page App)
    # ======================
    location / {
        proxy_pass http://localhost:8000;
        try_files $uri $uri/ /index.html;
    }

    # ======================
    # Security Headers
    # ======================
    add_header X-Frame-Options "ALLOW-FROM https://yourdomain.com";
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
    add_header Referrer-Policy "no-referrer-when-downgrade";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # ======================
    # Content Security Policy (CSP)
    # ======================
    add_header Content-Security-Policy "
        default-src 'self';
        script-src 'self';
        style-src 'self';
        img-src 'self' data: blob:;
        connect-src 'self' wss://<domain>;
        frame-ancestors https://yourdomain.com;
    ";

    # ======================
    # Gzip Compression
    # ======================
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
    gzip_proxied any;
    gzip_vary on;
}

# ================================
# Redirect HTTP to HTTPS
# ================================
server {
    listen 80;
    server_name <domain>;

    return 301 https://$host$request_uri;
}
