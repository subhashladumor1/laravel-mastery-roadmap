# 🧠 04 - Scalable SaaS Design: Multi-Tenancy

### ❓ The Interview Question
**"We have 10,000 corporate clients (Tenants), each with their own `users` and `products`. How do you design the database?"**

---

### 💡 High-Level Answer (Senior Architect)
"There are 3 main approaches. Each has trade-offs.
1.  **Single Database, Shared Schema**: Add `tenant_id` to every table. Simple, cheap, but risk of data leakage.
2.  **Single Database, Schemas (Postgres)**: Each tenant gets a schema (`schema_tenant_1.users`). Good isolation, but harder to migrate.
3.  **Multi-Database**: Each tenant gets a completely separate DB (`tenant_1_db`, `tenant_2_db`). Maximum isolation/security, but operationally complex (10,000 DB connections/migrations)."

**My Suggestion:**
"For 10k tenants, **Single DB Shared Schema** (`tenant_id`) is best unless strict compliance (HIPAA/GDPR) mandates physical separation. Use Row Level Security (RLS) or Global Scopes to prevent leaks.
Move large tenants to dedicated shards later."

---

### 💻 Code Example (Global Scope)

**Model Trait:** `app/Traits/BelongsToTenant.php`

```php
trait BelongsToTenant {
    protected static function bootBelongsToTenant()
    {
        if (check()) {
            static::addGlobalScope('tenant', function (Builder $builder) {
                $builder->where('tenant_id', session('current_tenant_id'));
            });
        }
    }
}
```

**Testing Isolation:**

```php
public function test_tenant_cannot_see_others_data()
{
    $tenantA = Tenant::factory()->create();
    $tenantB = Tenant::factory()->create();
    
    $productA = Product::factory()->create(['tenant_id' => $tenantA->id]);
    
    // Login as Tenant B
    actingAs($tenantB->owner);
    
    // Should return Nothing!
    $this->assertEmpty(Product::all()); 
}
```

---

### 🌍 Real World Scenario
**Scenario:**
Slack.
Each workspace (Tenant) has channels/users.
If `acme.slack.com` could see `google.slack.com` messages, it's game over.
Slack uses massive sharding architecture (Vitess/MySQL).

---

### 🔄 Follow-Up Question
**Q: "How do you handle migrations on 10,000 databases?"**
**A:** "We create a queue job for each tenant that runs `php artisan migrate --database=tenant_X`.
This runs asynchronously. We track migration status per tenant. We never run migrations synchronously on deployment for 10k DBs because it would timeout the deployment pipeline."
