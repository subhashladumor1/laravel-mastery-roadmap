# 🔐 10 - Security (Lock It Down)

> **"Security is not a feature, it's a state of mind."**
> Laravel is secure by default, but developers introduce vulnerabilities.
> Here we cover XSS, CSRF, CSP, and SQL Injection.

---

## 📚 Topics

| Topic | Description | Difficulty |
| :--- | :--- | :--- |
| **[01-xss-prevention.md](./01-xss-prevention.md)** | Stopping Cross-Site Scripting Attacks. | 🟢 Basic |
| **[02-csp-headers.md](./02-csp-headers.md)** | Content Security Policy (The final boss of security). | 🔴 Hard |
| **[03-secure-authentication.md](./03-secure-authentication.md)** | Session fixation, 2FA, throttling. | 🟠 Medium |
| **[04-sql-injection-prevention.md](./04-sql-injection-prevention.md)** | Why raw queries are dangerous. | 🟢 Basic |

---

## 💡 Quick Tips

1.  **Don't disable CSRF**: Keep `@csrf` in forms.
2.  **Escape Output**: Use `{{ $data }}` (escaped) not `{!! $data !!}` (raw).
3.  **Update**: Run `composer audit` regularly.
