# 📊 01 - Request Lifecycle Diagram

### 🧐 Explanation
This diagram shows the complete journey of an HTTP request.
From the moment it hits `public/index.php`, through the kernel, middleware stack, controller, and back to the user.

```mermaid
graph TD
    User([User Request]) --> Nginx[Web Server / Nginx]
    
    subgraph Laravel Application
        Nginx --> IndexPHP[public/index.php]
        IndexPHP --> HTTPS[HTTP Kernel Captured]
        
        HTTPS --> Boot[Bootstrappers]
        subgraph Bootstrapping
            Boot --> Env[Load .env]
            Boot --> Config[Load Config]
            Boot --> Exceptions[Handle Exceptions]
            Boot --> Facades[Register Facades]
            Boot --> Providers[Register Service Providers]
            Providers --> BootProviders[Boot Service Providers]
        end
        
        BootProviders --> Router[Router Dispatch]
        
        subgraph Middleware Stack
            Router --> GlobalMiddleware[Global Middleware]
            GlobalMiddleware --> GroupMiddleware[Route Group Middleware]
            GroupMiddleware --> Route[Route Specific Middleware]
        end
        
        Route --> Controller[Controller Action]
        
        Controller -->|Return| Response[Response Object]
        
        Response -->|Send| IndexPHP
        
        subgraph Termination
            IndexPHP --> Terminate[Kernel Terminate]
            Terminate --> MidTerminate[Middleware Terminate]
        end
    end
    
    IndexPHP -->|HTTP 200| User
```
