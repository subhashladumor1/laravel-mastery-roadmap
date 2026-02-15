# 🌐 14 - Laravel Ecosystem: The First-Party Tools

> **"Laravel is more than a framework. It's a universe."**
> These are official packages maintained by the Laravel team.

---

## 📚 Topics

| Topic | Description | Difficulty |
| :--- | :--- | :--- |
| **[01-sanctum-auth.md](./01-sanctum-auth.md)** | SPA & Mobile Auth made simple. | 🟢 Basic |
| **[02-scout-search.md](./02-scout-search.md)** | Full-text search with Algolia/Meilisearch. | 🟠 Medium |
| **[03-telescope-debug.md](./03-telescope-debug.md)** | The ultimate debugger for your local machine. | 🟢 Basic |
| **[04-socialite-login.md](./04-socialite-login.md)** | Login with Google/GitHub/Facebook. | 🟢 Basic |

---

## 💡 Quick Tips

1.  **Don't use Passport**: Unless you are building an OAuth2 Server (like GitHub API). Use Sanctum.
2.  **Telescope**: Don't leave it enabled in Production without pruning (it fills DB fast).
3.  **Scout**: Use `database` driver for small projects (MySQL LIKE %).
