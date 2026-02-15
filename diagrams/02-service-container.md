# 📊 02 - Service Container Resolution

### 🧐 Explanation
How does `app(UserContract::class)` become `new UserService()`?
This diagram shows the recursive resolution logic.

```mermaid
graph TD
    UserCode[User Controller: @inject UserService] -->|app()| Container[Service Container]
    
    subgraph Container Resolution
        Container --> Bind[Is Bound?]
        
        Bind -->|Yes| Constructor[Fetch Concrete Class via Binding]
        Bind -->|No| Reflection[Use Reflection (Auto Wiring)]
        
        Reflection --> Params[Has Constructor?]
        
        Params -->|No| Create[Create Instance (new UserService)]
        Params -->|Yes| Deps[Dependencies?]
        
        Deps -->|Yes| Recursive[Resolve Dependencies Recursively]
        Recursive -->|For Each| Container
        
        Deps -->|No| Create
    end
    
    Create -->|Return| UserCode
```
