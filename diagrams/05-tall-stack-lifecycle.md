# 🎨 TALL Stack Architecture (Tailwind, Alpine, Laravel, Livewire)

> **"The Monolith Feel, The SPA Power."**
> Understanding how Livewire hydrates the DOM and communicates with the server.

---

## 🟢 1. The Initial Load (Server Side Rendering)
Livewire renders the initial HTML on the server, just like a standard Blade view.

```mermaid
graph TD
    User[Browser Request /dashboard] --> Laravel[Laravel Router]
    Laravel --> Controller[DashboardController]
    Controller --> Component[Livewire Component mount]
    Component --> Blade[Render Blade View]
    Blade --> User[Return HTML + Initial State used by Alpine]
```

## 🔄 2. The Lifecycle (User Interaction)
When a user types or clicks, Livewire sends an AJAX request.

```mermaid
graph LR
    User[User Clicks Button] --> JS[Livewire JS Intercept]
    JS -->|Fetch| Server[Server Endpoint /livewire/message]
    Server -->|Hydrate| Component[Hydrate PHP Component]
    Component -->|Action| Method[Run public function save]
    Method -->|Dehydrate| Response[Return HTML Diff + New State]
    Response -->|Morph| DOM[Alpine/Morphdom Updates DOM]
```

## 🤝 3. Alpine.js Integration (Entangle)
Sharing state between PHP and JavaScript without network requests (for some parts).

```mermaid
graph TD
    PHP[Livewire count] <-->|@entangle| Alpine[Alpine x-data count]
    Alpine -->|Immediate Update| UI[Update span instantly]
    Note[Network Request happens in background if needed]
```

## ⚡ 4. Real-Time Broadcasting (Echo)
Listening for server events.

```mermaid
graph LR
    Server[Event Fired: OrderShipped] -->|Pusher/Reverb| WebSocket
    WebSocket -->|Listen| Livewire[Livewire Component]
    Livewire -->|Function| Refresh[this->refresh]
```
