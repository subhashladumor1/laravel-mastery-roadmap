# 🍼 Intermediate: Request Lifecycle (How it works?)

## ❓ The Question
**"Trace the lifecycle of a Laravel Request from `index.php` to Response."**

---

### 💡 The Answer (Intermediate)
1.  **Entry Point**: `public/index.php`. The web server (Nginx) sends the request here.
2.  **Kernel**: The Request is captured by `Illuminate\Foundation\Http\Kernel`.
3.  **Service Providers**: The Application boots. It loads config (`config/app.php`), registers services (`AppServiceProvider`), connects to DB.
4.  **Router**: The Kernel sends request to the Router (`routes/web.php`).
5.  **Middleware**: Before hitting the Controller, it passes through Global Middleware (`EnsureFrontendRequestsAreStateful`) and Route Middleware (`auth`).
6.  **Controller**: The Action executes (`UserController@index`).
7.  **Response**: The Controller returns a View or JSON.
8.  **Kernel Terminate**: The Kernel sends response to browser and terminates (runs `terminate()` on middleware).

**Why this matters?**
"Understanding this helps debug weird issues like 'Why is my config null?' (Because it loads before providers) or 'Why is Auth user null?' (Because middleware hasn't run yet)."

---

### 💻 Visualize It

```
 Request (Browser)
    |
 V (Nginx)
    |
 V public/index.php
    |
 V Kernel (HTTP) -> BOOTSTRAP (Load .env, Config, Facades, Providers)
    |
 V Router (Find Route)
    |
 V Middleware (Auth Check)
    |
 V Controller (Logic)
    |
 V View (Blade)
    |
 V Response (Browser)
```
