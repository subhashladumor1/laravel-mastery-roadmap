# ⚡ 50+ Intermediate Laravel Interview Questions (Rapid Fire)

> **Instructions**: These cover Relationships, Middleware, API, and Deeper Eloquent concepts.

---

### 🟠 Eloquent Relationships
1.  **What is a Many-to-Many relationship?**
    *   Example: Users and Roles. A user has multiple roles, a role has multiple users.
2.  **What helper creates a One-to-Many model?**
    *   `hasMany(Comment::class)`
3.  **What table is needed for Many-to-Many?**
    *   A Pivot Table (e.g., `role_user`).
4.  **How do you access pivot columns?**
    *   `$model->pivot->column_name`
    *   (Requires `withPivot('column_name')`).
5.  **What is a Polymorphic Relationship?**
    *   Example: Comments on both `Video` and `Post` (using `commentable_id`, `commentable_type`).
6.  **How do you eager load multiple relationships?**
    *   `Post::with('comments', 'author')->get()`
7.  **What is Lazy Eager Loading?**
    *   `$post->load('comments')` (After model is fetched).
8.  **How do you query existence for a relationship?**
    *   `Post::has('comments')->get()` (Posts with at least 1 comment).
9.  **How do you filter based on relationship criteria?**
    *   `Post::whereHas('comments', function ($q) { $q->where('spam', false); })->get()`
10. **What is `touch()` used for?**
    *   Updates the `updated_at` timestamp of the parent model.

---

### 🟠 Advanced Queries
11. **How do you execute a raw SQL expression in Eloquent?**
    *   `DB::raw('COUNT(*) as total')`
12. **How do you select specific columns in Eloquent?**
    *   `User::select('id', 'name')->get()`
13. **How do you join tables manually?**
    *   `DB::table('users')->join('posts', 'users.id', '=', 'posts.user_id')`
14. **What is a Scope?**
    *   A reusable query constraint (e.g., `scopeActive($query)`).
15. **How do you utilize a Global Scope?**
    *   Define query constraints automatically applied to all queries on a model.
16. **How do you remove a specific Global Scope?**
    *   `User::withoutGlobalScope(ActiveScope::class)->get()`
17. **How do you chunk large datasets?**
    *   `User::chunk(100, function ($users) { ... })`
18. **How do you paginate manually?**
    *   `$items = new Paginator($array, 15);`
    *   (Or `LengthAwarePaginator`).
19. **How do you prevent N+1 Queries?**
    *   Use Eager Loading (`with()`).
20. **How do you check duplicates in a query?**
    *   `User::distinct()->get()`

---

### 🟠 Middleware & Routing
21. **How do you apply middleware to a specific route?**
    *   `Route::get(...)->middleware('auth');`
22. **How do you construct a middleware group?**
    *   Define in `Kernel.php` (e.g., `web`, `api`).
23. **What is Terminable Middleware?**
    *   Middleware that runs `terminate()` after the response is sent to the client (for logging).
24. **How do you define explicit route model binding?**
    *   `Route::bind('user', function ($value) { return User::...; })`
25. **How do you access route parameters in a controller?**
    *   `public function show(Request $request, $id)`
26. **How do you prioritize routes?**
    *   Define specific routes first (e.g., `/users/create` before `/users/{id}`).
27. **What is Rate Limiting?**
    *   Restricting requests per minute (`throttle:60,1`).
28. **How do you define a fallback route (404)?**
    *   `Route::fallback(function () { return view('404'); });`
29. **How do you cache routes?**
    *   `php artisan route:cache`
30. **How do you clear route cache?**
    *   `php artisan route:clear`

---

### 🟠 API & Responses
31. **How do you return immediate JSON?**
    *   `return response()->json(['data' => []]);`
32. **What is an API Resource Class?**
    *   A transformation layer between Models and JSON output.
33. **How do you return a 401 Unauthorized?**
    *   `return response()->json(['error' => 'Unauthorized'], 401);`
34. **How do you paginate API resources?**
    *   `UserResource::collection(User::paginate(10))`
35. **How do you modify headers?**
    *   `response('Hello')->header('Content-Type', 'text/plain')`
36. **How do you attach cookies?**
    *   `response('Hello')->cookie('name', 'value', $minutes)`
37. **How do you protect API routes?**
    *   `auth:sanctum` or `auth:api` middleware.
38. **How do you download a file?**
    *   `return Storage::download('file.jpg');`
39. **How do you show a file in browser (inline)?**
    *   `return response()->file($path);`
40. **How do you validate JSON input?**
    *   `$request->validate(['field' => 'required']);` (Laravel detects JSON content-type automatically).

---

### 🟠 Storage & Files logic
41. **What is the local disk driver?**
    *   Stores files in `storage/app`.
42. **What is the public disk driver?**
    *   Stores files in `storage/app/public` (Symlinked to `public/storage`).
43. **How do you upload a file?**
    *   `$request->file('avatar')->store('avatars');`
44. **How do you get storage URL?**
    *   `Storage::url('avatars/file.jpg')`
45. **How do you delete a file?**
    *   `Storage::delete($path)`
46. **How do you check if a file exists?**
    *   `Storage::exists($path)`
47. **How do you write content to a file?**
    *   `Storage::put('file.txt', 'Content');`
48. **How to generate temporary URLs (S3)?**
    *   `Storage::temporaryUrl($path, now()->addMinutes(5))`
49. **How do you configure multiple filesystems?**
    *   `config/filesystems.php`
50. **What command links storage?**
    *   `php artisan storage:link`
