# 📊 04 - Job Queue Architecture

### 🧐 Explanation
How does `dispatch(new SendWelcomeEmail)` relate to `php artisan queue:work`?
From producer (Controller) to Consumer (Worker).

```mermaid
graph TD
    UserCode[User Controller: dispatch(SendEmail)] -->|Serialize Job| AppCode[Job Object]
    
    subgraph Producer (Request)
        AppCode --> Connect[Database/Redis Connection]
        Connect --> Push[Push to 'default' Queue]
    end
    
    subgraph Storage (Queue)
        Push --> Redis[Redis / Database]
        Redis -->|Wait| Pending[Pending Jobs List]
    end
    
    subgraph Consumer (Worker)
        Redis -->|Pop (BLPOP)| Worker[php artisan queue:work]
        Worker --> Deserialize[Unserialize Job Object]
        
        Deserialize -->|Execute| Handler[SendEmail->handle()]
        
        subgraph Outcome
            Handler --> Success[Success?]
            Success -->|Yes| Complete[Delete Job from Queue]
            Success -->|No| FailChecks[Tries < Retries?]
            
            FailChecks -->|Yes| Re-Queue[Release back to Redis (with delay)]
            FailChecks -->|No| FailTable[Insert into Failed Jobs Table]
        end
    end
```
