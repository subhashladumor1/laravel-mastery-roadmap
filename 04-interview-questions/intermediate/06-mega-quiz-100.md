# ⚡ Intermediate Mega-List (100 Questions)

> **"Beyond the Basics."**
> These 100 questions test your knowledge of Relationships, Optimization, Services, and Architecture.

---

### 🟠 01-10: Relationships (Deep Dive)
1.  **Inverse of `hasOne`?**
    *   `belongsTo`
2.  **Inverse of `hasMany`?**
    *   `belongsTo`
3.  **Inverse of `belongsToMany`?**
    *   `belongsToMany`
4.  **Relationship via intermediate table?**
    *   `hasManyThrough`
5.  **Polymorphic One-to-One?**
    *   `morphOne`
6.  **Polymorphic One-to-Many?**
    *   `morphMany`
7.  **Polymorphic Many-to-Many?**
    *   `morphToMany`
8.  **Get pivot table data?**
    *   `$user->roles->pivot->created_at`
9.  **Filter by relationship existence?**
    *   `User::has('posts')`
10. **Filter by relationship constraint?**
    *   `User::whereHas('posts', fn($q) => $q->where('published', 1))`

### 🟠 11-20: Optimization & Eloquent
11. **Eager Load relationship?**
    *   `User::with('posts')->get()`
12. **Eager Load specific columns?**
    *   `User::with('posts:id,title,user_id')->get()`
13. **Count relationship without loading?**
    *   `User::withCount('posts')->get()`
14. **Lazy Eager Load?**
    *   `$user->load('posts')`
15. **Prevent N+1 globally?**
    *   `Model::preventLazyLoading()`
16. **Select specific columns?**
    *   `select('id', 'name')`
17. **Chunk large results?**
    *   `User::chunk(100, function($users) { ... })`
18. **Stream large results?**
    *   `User::lazy()` (LazyCollection)
19. **Mass Assignment Exception?**
    *   Thrown if field not in `$fillable` (on strict mode).
20. **Use UUIDs instead of IDs?**
    *   Use `HasUuids` trait.

### 🟠 21-30: Database & Migrations
21. **Add index to column?**
    *   `$table->string('email')->index()`
22. **Add foreign key constraint?**
    *   `$table->foreignId('user_id')->constrained()`
23. **Cascade on delete?**
    *   `->onDelete('cascade')`
24. **Soft Deletes migration?**
    *   `$table->softDeletes()`
25. **Rollback last batch?**
    *   `migrate:rollback`
26. **Rollback everything?**
    *   `migrate:reset`
27. **Refresh database (wipe & run)?**
    *   `migrate:refresh` (or `migrate:fresh`)
28. **Run seeder after migration?**
    *   `migrate --seed`
29. **Create specific migration?**
    *   `make:migration add_votes_to_users_table`
30. **Raw SQL query?**
    *   `DB::select('select * from users')`

### 🟠 31-40: Middleware & Requests
31. **Middleware signature?**
    *   `handle(Request $request, Closure $next)`
32. **Before middleware?**
    *   Code before `$next($request)`
33. **After middleware?**
    *   Code after `$next($request)`
34. **Route-specific middleware?**
    *   Assign key in `Kernel::$routeMiddleware`
35. **Group middleware?**
    *   Assign key in `Kernel::$middlewareGroups`
36. **Global middleware?**
    *   In `Kernel::$middleware`
37. **Rate Limiting definition?**
    *   `RateLimiter::for('api', ...)`
38. **Throttle middleware usage?**
    *   `throttle:60,1` (60 reqs / 1 min)
39. **Form Request validation rules?**
    *   `rules()` method returning array.
40. **Authorization in Form Request?**
    *   `authorize()` method returning bool.

### 🟠 41-50: API Resources
41. **Resource class command?**
    *   `make:resource UserResource`
42. **Convert model to array?**
    *   `toArray($request)` method.
43. **Return single resource?**
    *   `new UserResource($user)`
44. **Return collection of resources?**
    *   `UserResource::collection($users)`
45. **Conditional attribute?**
    *   `$this->when($condition, 'value')`
46. **Conditional relationship?**
    *   `$this->whenLoaded('posts')`
47. **Wrap data in 'data' key?**
    *   Default behavior.
48. **Disable wrapping?**
    *   `JsonResource::withoutWrapping()`
49. **Pagination metadata?**
    *   Automatically added for `paginate()`.
50. **Customize response headers?**
    *   `->response()->header(...)`

### 🟠 51-60: Storage & Files
51. **Config file?**
    *   `config/filesystems.php`
52. **Default disk?**
    *   `local`
53. **Store file from request?**
    *   `$request->file('avatar')->store('path')`
54. **Store on S3?**
    *   `->store('path', 's3')`
55. **Get file URL?**
    *   `Storage::url($path)`
56. **Get file contents?**
    *   `Storage::get($path)`
57. **Check existence?**
    *   `Storage::exists($path)`
58. **Delete file?**
    *   `Storage::delete($path)`
59. **Download file?**
    *   `Storage::download($path)`
60. **Link public storage?**
    *   `php artisan storage:link`

### 🟠 61-70: Queues & Jobs
61. **Create job?**
    *   `make:job SendEmail`
62. **Dispatch job?**
    *   `dispatch(new SendEmail)`
63. **Interface for queueing?**
    *   `ShouldQueue`
64. **Delay dispatch?**
    *   `->delay(now()->addMinutes(5))`
65. **Process jobs command?**
    *   `queue:work`
66. **Retry failed job?**
    *   `queue:retry {id}`
67. **List failed jobs?**
    *   `queue:failed`
68. **Specify queue connection?**
    *   `onConnection('redis')`
69. **Specify queue name?**
    *   `onQueue('emails')`
70. **Chain jobs?**
    *   `Bus::chain([new JobA, new JobB])->dispatch()`

### 🟠 71-80: Events & Listeners
71. **Create event?**
    *   `make:event OrderPlaced`
72. **Create listener?**
    *   `make:listener SendEmail`
73. **Register event/listener?**
    *   `EventServiceProvider` (or auto-discovery).
74. **Dispatch event?**
    *   `event(new OrderPlaced($order))`
75. **Queue listener?**
    *   Implement `ShouldQueue`.
76. **Where is event data?**
    *   Public properties of Event class.
77. **Model Observers?**
    *   `created`, `updated`, `deleted` hooks.
78. **Create observer?**
    *   `make:observer UserObserver`
79. **Register observer?**
    *   `User::observe(UserObserver::class)`
80. **Suppress events?**
    *   `User::withoutEvents(function() { ... })`

### 🟠 81-90: Testing (PHPUnit/Pest)
81. **Run tests?**
    *   `php artisan test`
82. **Create test?**
    *   `make:test UserTest`
83. **Refresh database trait?**
    *   `RefreshDatabase`
84. **Assert status 200?**
    *   `$response->assertOk()`
85. **Assert redirect?**
    *   `$response->assertRedirect('/home')`
86. **Assert view has data?**
    *   `$response->assertViewHas('users')`
87. **Assert database has row?**
    *   `$this->assertDatabaseHas('users', ['email' => '...'])`
88. **Act as user?**
    *   `$this->actingAs($user)`
89. **Mocking facade?**
    *   `Storage::fake('local')`
90. **Mocking event?**
    *   `Event::fake()`

### 🟠 91-100: Miscellaneous
91. **Service Container?**
    *   Manages dependency injection.
92. **Service Provider?**
    *   Bootstraps application services.
93. **Facade?**
    *   Static interface to container classes.
94. **Helper function?**
    *   Global functions (`app()`, `collect()`).
95. **Collection?**
    *   Wrapper for arrays with methods (`map`, `filter`).
96. **Localization?**
    *   `lang/` directory and `__('key')` helper.
97. **Scheduling?**
    *   `routes/console.php` (Kernel schedule).
98. **Commands?**
    *   `app/Console/Commands`.
99. **Mailables?**
    *   Classes for sending email (`Mail::to()`).
100. **Notifications?**
     *   Short messages via multiple channels (Mail, Slack, SMS).
