# 💰 Advanced Task: High Traffic API (5,000 req/sec)

### 📌 Problem Description
**"Build a simple public GET /api/stats endpoint. It sounds easy, but it will be hit by 500,000 bots per minute."**

**Core Requirements:**
1.  **Read Heavy**: Most requests are `SELECT COUNT(*) FROM users;`.
2.  **No Blocking**: DB will die if 1000 processes hit it simultaneously.
3.  **Throttling**: Rate limit 60 req/min per IP.
4.  **Stale-While-Revalidate**: Serve old cache while generating new one in bg.
5.  **Headers**: Proper `Cache-Control` for CDNs (Cloudflare).

---

### 📐 Architecture Diagram (ASCII)

```
[ User ] --> [ Cloudflare CDN ]
                    |
            [ Nginx (Load Balancer) ]
                    |
           [ Laravel (API) ]
           /                \
   [ Redis Cache ]      [ MySQL Replica ]
```

---

### 💻 Implementation Guide

**1. The Cache strategy (Atomic Lock):**

**File:** `app/Http/Controllers/StatsController.php`

```php
public function index()
{
    // 1. Try Cache First (Fast Path: 2ms)
    $stats = Cache::get('global_stats');

    if ($stats) {
        // 2. Background Refresh? (If cache is older than 5 mins)
        if (Cache::get('global_stats_stale')) {
            dispatch(new RefreshStatsJob()); 
        }
        
        return response()->json($stats)
            ->header('Cache-Control', 'public, max-age=60');
    }

    // 3. Cache Miss (Slow Path: 200ms) - Only 1 process allowed!
    return Cache::lock('stats_generation', 10)->get(function () {
        
        $data = [
            'users' => User::count(),
            'orders' => Order::sum('amount'),
        ];
        
        Cache::put('global_stats', $data, 600); // 10 mins
        Cache::put('global_stats_stale', true, 300); // 5 mins fresh
        
        return response()->json($data);
    });
}
```

**2. Rate Limiting (Redis):**

**File:** `app/Providers/RouteServiceProvider.php`

```php
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)->by($request->ip());
});
```

---

### 🧪 Test Cases (Pest PHP)

**Test 1: Cache Hit**
```php
it('serves cached response', function () {
    Cache::shouldReceive('get')->once()->andReturn(['users' => 100]);
    
    getJson('/api/stats')->assertJson(['users' => 100]);
});
```

---

### 🚀 Deployment Steps

1.  **Nginx**: Configure `fastcgi_cache` if possible.
2.  **Redis**: Ensure `maxmemory-policy allkeys-lru`.
3.  **Horizon**: Run 10+ workers for `RefreshStatsJob`.
