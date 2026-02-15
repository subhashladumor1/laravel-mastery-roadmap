# 🐳 12 - DevOps: Deployment & Infrastructure

> **"It works on my machine"** is not a valid deployment strategy.
> We cover Docker, Nginx, Supervisor, and Redis configuration for production.

---

## 📚 Topics

| Topic | Description | Difficulty |
| :--- | :--- | :--- |
| **[01-docker-setup.md](./01-docker-setup.md)** | `docker-compose.yml` for Laravel, MySQL, Redis. | 🟢 Basic |
| **[02-nginx-config.md](./02-nginx-config.md)** | Configuring `site.conf` for PHP-FPM, SSL, and Gzip. | 🟠 Medium |
| **[03-supervisor-horizon.md](./03-supervisor-horizon.md)** | Keeping your queues alive 24/7. | 🟠 Medium |
| **[04-redis-setup.md](./04-redis-setup.md)** | Configuring Redis for Cache, Session, and Queues. | 🟢 Basic |

---

## 💡 Quick Tips

1.  **Use Laravel Sail**: For local development.
2.  **Use Forge/Envoyer**: For easy VPS management if you aren't a DevOps expert.
3.  **Environment Variables**: Never commit `.env`.
