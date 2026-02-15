# 🐳 02 - Nginx Config (Laravel Optimized)

### 🧐 Explanation
Nginx is the **Web Server**. PHP-FPM is the **Processor**.
Nginx receives `GET /` -> Sends to `index.php` -> PHP-FPM executes -> Nginx sends HTML.

**Critical Settings:**
1.  **Safety**: Hide `.env` access.
2.  **Performance**: Enable Gzip/Brotli.
3.  **HMR**: Enable WebSocket Proxy for Vite.
4.  **SSL**: Let's Encrypt / Certbot.

---

### 💻 `site.conf` (Best Practice)

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name example.com;
    root /var/www/html/public; # IMPORTANT: Point to /public

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";
    add_header X-XSS-Protection "1; mode=block";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    index index.php;

    charset utf-8;

    # Gzip Compression
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types text/plain text/css text/xml application/json application/javascript application/rss+xml application/atom+xml image/svg+xml;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock; # Or 127.0.0.1:9000
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_read_timeout 300; # 5 minutes for long uploads
    }

    # Hide Critical Files
    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

---

### ✅ When to Use
1.  **VPS (DigitalOcean/Linode)**: Manually setup Nginx.
2.  **Docker**: `php:8.3-fpm` container usually needs a separate Nginx container with this config.

### ❌ When NOT to Use
1.  **Serverless**: AWS Lambda handles this differently (Bref).
2.  **Shared Hosting**: Usually Apache `.htaccess`. Don't use Apache. Migrate to Nginx if possible. It's faster.

---

### 🎙 Interview Insight
**Q: "Why do we point `root` to `/public` and not the project root?"**
**A:** "Security (Standard Directory Layout).
If `root` pointed to `/var/www/html/`, users could visit `example.com/.env` and steal my secrets.
By pointing to `/public`, only `index.php` and assets are exposed. The Laravel framework code (`app/`, `.env`, `vendor/`) is completely inaccessible via HTTP."
