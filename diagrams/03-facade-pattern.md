# 📊 03 - The Facade Pattern

### 🧐 Explanation
How does `Auth::user()` work?
It's just a static method (`__callStatic`) on a class (`Illuminate\Support\Facades\Auth`) that resolves an object (`auth` binding) from the Container.

```mermaid
graph TD
    UserCode[User: Auth::check()] --> Facade[Auth Facade]
    Facade --> CallStatic[__callStatic()]
    
    subgraph Facade Internal
        CallStatic --> Accessor[getFacadeAccessor]
        Accessor -->|returns| Binding[auth]
        
        Binding -->|resolve| Container[Service Container]
        Container -->|make('auth')| Instance[AuthManager (Singletone)]
    end
    
    Instance -->|check| Method[AuthManager check]
    Method -->|Return true| Facade
    Facade -->|Return true| UserCode
```
