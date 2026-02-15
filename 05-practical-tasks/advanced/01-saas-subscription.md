# 🩸 Advanced Task: SaaS Subscription (Multi-Tenant)

### 📌 Problem Description
**"Build a scalable Multi-Tenant SaaS platform where users can subscribe to different plans (Basic, Pro, Enterprise). Each plan unlocks specific features and limits."**

**Core Requirements:**
1.  **Subscription Handling**: Use Stripe/Paddle for payments.
2.  **Feature Flags**: Access control based on plan limits (e.g., Basic: 5 projects, Pro: Unlimited).
3.  **Webhook Reliability**: Handle Stripe webhooks securely (Signature verification, Idempotency).
4.  **Grace Periods**: Handle failed payments gracefully without locking users out immediately.
5.  **Database**: Tenancy (Team-based or User-based).

---

### 📐 Architecture Diagram (ASCII)

```
[ User ] --> [ Load Balancer (Nginx) ]
                    |
            [ Laravel API ]
            /       |       \
[ Stripe Webhook ]  |  [ MySQL (Data + Features) ]
       |            |
 [ Queue: Webhooks ]|
                    |
             [ Redis (Cache Limits) ]
```

---

### 💻 Implementation Guide

**1. Service Layer Design**
Do not put Stripe logic in Controllers! Use a dedicatd `SubscriptionService`.

**File:** `app/Services/SubscriptionService.php`

```php
namespace App\Services;

use App\Models\Team;
use Laravel\Cashier\Subscription;

class SubscriptionService
{
    public function subscribe(Team $team, string $planId, string $paymentMethod): Subscription
    {
        // 1. Create or Update Customer in Stripe
        $team->createOrGetStripeCustomer();

        // 2. Clear Plan Cache (Crucial for performance!)
        cache()->forget("team_features_{$team->id}");

        // 3. Initiate Subscription
        return $team->newSubscription('default', $planId)
            ->create($paymentMethod);
    }

    public function cancel(Team $team): void
    {
        $team->subscription('default')->cancel(); // Sets 'ends_at' date
    }
}
```

**2. Feature Middleware**
Enforce limits globally.

**File:** `app/Http/Middleware/CheckprojectLimit.php`

```php
public function handle(Request $request, Closure $next)
{
    $team = $request->user()->currentTeam;
    $maxProjects = config("plans.{$team->plan}.max_projects");

    if ($team->projects()->count() >= $maxProjects) {
        abort(403, "Upgrade your plan to create more projects.");
    }

    return $next($request);
}
```

---

### 🧪 Test Cases (Pest PHP)

**Test 1: Plan Limits Enforced**
```php
it('prevents creating projects over limit', function () {
    $user = User::factory()->create();
    $team = Team::factory()->create(['owner_id' => $user->id, 'plan' => 'basic']); // Limit: 5
    
    Project::factory()->count(5)->create(['team_id' => $team->id]);

    actingAs($user)
        ->postJson('/api/projects', ['name' => 'Over Limit'])
        ->assertForbidden();
});
```

**Test 2: Webhook Idempotency**
```php
it('ignores duplicate webhook events', function () {
    $event = StripeEvent::factory()->create(['stripe_id' => 'evt_123']);
    
    // Process first time
    postJson('/stripe/webhook', $event->payload)->assertOk();
    
    // Process second time (should still be OK but do nothing)
    postJson('/stripe/webhook', $event->payload)->assertOk();
});
```

---

### 🚀 Deployment Steps

1.  **Environment Config**:
    *   Set `STRIPE_KEY` and `STRIPE_SECRET`.
    *   Set `STRIPE_WEBHOOK_SECRET` (Crucial!).
2.  **Queue Workers**:
    *   Ensure `php artisan queue:work` is running (Webhooks process asynchronously).
    *   Use Supervisor: `numprocs=2`.
3.  **Database Migration**:
    *   `php artisan migrate --force`.
4.  **Cache**:
    *   `php artisan config:cache`.
