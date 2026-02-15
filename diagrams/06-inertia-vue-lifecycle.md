# 🌐 VILT Stack Architecture (Vue/React, Inertia, Laravel, Tailwind)

> **"The Modern Monolith."**
> Understanding how Inertia bridges the gap between Laravel Routing and Vue Components.

---

## 🟢 1. The Initial Load (Server-Side)
When visiting `/dashboard`.

```mermaid
graph TD
    User[Browser Requests /dashboard] --> Laravel[Laravel Router]
    Laravel --> Controller[DashboardController]
    Controller --> Inertia[Returns Inertia::render('Dashboard')]
    Inertia --> JSON[Combines Props + View Layout]
    JSON --> Blade[app.blade.php + Initial Props Data]
    Blade --> Start[Hydrate Vue application]
    Start --> Render[Render Vue Component with Initial Data]
```

## 🔄 2. The Inertia Link (Client-Side Navigation)
When clicking `<Link href="/users" />`.

```mermaid
graph LR
    User[Click Link] --> InertiaJS[Inertia.js Intercepts]
    InertiaJS -->|AJAX XHR| Server[Server Endpoint /users]
    Server -->|Detect Inertia| Controller[UsersController]
    Controller -->|Return JSON| InertiaJS[Receive Props { users: [...] }]
    InertiaJS -->|Swap Component| Vue[Replace Dashboard.vue with Users.vue]
    Note[No Full Page Reload!]
```

## 📦 3. Data Rehydration (Partial Reloads)
When fetching specific data (e.g., Lazy Loading).

```mermaid
graph TD
    Vue[mounted()] --> Fetch[Inertia.reload({ only: ['stats'] })]
    Fetch -->|Request| Server
    Server -->|Compute| Stats[Only Compute Stats]
    Server -->|Response| JSON[Return Only Stats]
    JSON -->|Update| VueProp[Merge with existing Props]
```

## ⚡ 4. Shared Data (Global Props)
Middleware handling Auth/Flash messages.

```mermaid
graph LR
    Request --> Middleware[HandleInertiaRequests]
    Middleware --> global[Define 'auth.user' & 'flash']
    Middleware --> Controller[Specific Page Props]
    Controller --> Response[Merged Object { auth, flash, posts }]
```
