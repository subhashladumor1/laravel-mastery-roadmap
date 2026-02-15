# 🐳 01 - Docker Setup (Development)

### 🧐 Explanation
Docker creates a **Container** (Virtual Machine) that includes:
1.  **PHP 8.3**
2.  **Nginx**
3.  **MySQL 8.0**
4.  **Redis 7.0**

**Laravel Sail** is a CLI wrapper for Docker.
`./vendor/bin/sail up` = Starts everything.

---

### 💻 `docker-compose.yml` (Manual Setup)

If you don't use Sail, here is a minimal `docker-compose.yml` for Laravel.

```yaml
services:
    laravel.test:
        build:
            context: ./vendor/laravel/sail/runtimes/8.3
            dockerfile: Dockerfile
            args:
                WWWGROUP: '${WWWGROUP}'
        image: sail-8.3/app
        extra_hosts:
            - 'host.docker.internal:host-gateway'
        ports:
            - '${APP_PORT:-80}:80'
            - '${VITE_PORT:-5173}:5173'
        environment:
            WWWUSER: '${WWWUSER}'
            LARAVEL_SAIL: 1
            XDEBUG_MODE: '${SAIL_XDEBUG_MODE:-off}'
            XDEBUG_CONFIG: '${SAIL_XDEBUG_CONFIG:-client_host=host.docker.internal}'
        volumes:
            - '.:/var/www/html'
        networks:
            - sail
        depends_on:
            - mysql
            - redis

    mysql:
        image: 'mysql/mysql-server:8.0'
        ports:
            - '${FORWARD_DB_PORT:-3306}:3306'
        environment:
            MYSQL_ROOT_PASSWORD: '${DB_PASSWORD}'
            MYSQL_ROOT_HOST: "%"
            MYSQL_DATABASE: '${DB_DATABASE}'
            MYSQL_USER: '${DB_USERNAME}'
            MYSQL_PASSWORD: '${DB_PASSWORD}'
        volumes:
            - 'sail-mysql:/var/lib/mysql'
        networks:
            - sail
        healthcheck:
            test: ["CMD", "mysqladmin", "ping", "-p${DB_PASSWORD}"]
            retries: 3
            timeout: 5s

    redis:
        image: 'redis:alpine'
        ports:
            - '${FORWARD_REDIS_PORT:-6379}:6379'
        volumes:
            - 'sail-redis:/data'
        networks:
            - sail
        healthcheck:
            test: ["CMD", "redis-cli", "ping"]
            retries: 3
            timeout: 5s

networks:
    sail:
        driver: bridge

volumes:
    sail-mysql:
        driver: local
    sail-redis:
        driver: local
```

---

### ✅ When to Use
1.  **Team Projects**: Everyone runs `sail up`. No "It works on my Windows laptop but not your Mac" issues.
2.  **CI/CD**: GitHub Actions runs tests inside this container.

### ❌ When NOT to Use
1.  **Production**: `docker-compose` is okay for small VPS, but for scale, use **Kubernetes (K8s)** or Managed Services (AWS RDS, ElastiCache).

---

### 🎙 Interview Insight
**Q: "What is the difference between `php artisan serve` and Docker?"**
**A:** "`php artisan serve` uses PHP's built-in single-threaded web server. It's fine for `localhost` but cannot handle multiple concurrent requests (unless using Octane) and doesn't simulate Nginx/Redis configuration.
Docker simulates the **Checking Environments** exactly. If Nginx config is wrong, it breaks in Docker, allowing me to fix it *before* deploying to production."
