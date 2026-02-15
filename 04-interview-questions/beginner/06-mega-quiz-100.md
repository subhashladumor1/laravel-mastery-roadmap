# ⚡ Beginner Mega-List (100 Questions)

> **"Master the Basics."**
> These 100 questions cover everything from PHP syntax to basic Laravel routing.

---

### 🟢 01-10: PHP Foundation
1.  **What does PHP stand for?**
    *   Hypertext Preprocessor (Recursive acronym).
2.  **How do you start a PHP block?**
    *   `<?php`
3.  **How do you end a PHP block?**
    *   `?>` (Optional if file is pure PHP).
4.  **How do you define a constant?**
    *   `define('NAME', 'Value');` or `const NAME = 'Value';`
5.  **What is the difference between `'` and `"` quotes?**
    *   `"` parses variables (`$var`). `'` treats them as literal strings.
6.  **How do you comment a single line?**
    *   `//` or `#`.
7.  **How do you debug a variable and die?**
    *   `dd($var);` (Laravel) or `var_dump($var); die();` (PHP).
8.  **What is an Array?**
    *   A map that holds multiple values (`$arr = [1, 2, 3];`).
9.  **What is an Associative Array?**
    *   An array with named keys (`$arr = ['name' => 'John'];`).
10. **What loop is best for iterating arrays?**
    *   `foreach ($arr as $item) {}`

### 🟢 11-20: MVC & Laravel Structure
11. **What is MVC?**
    *   Model-View-Controller.
12. **Where are Controllers located?**
    *   `app/Http/Controllers`
13. **Where are Models located?**
    *   `app/Models`
14. **Where are Views located?**
    *   `resources/views`
15. **Where is the configuration?**
    *   `config/` directory.
16. **Where are database migrations?**
    *   `database/migrations`
17. **Where are static assets (images/css)?**
    *   `public/`
18. **Where are routes defined?**
    *   `routes/`
19. **What is `.env`?**
    *   Environment variables (DB credentials, API keys).
20. **Should `.env` be committed to Git?**
    *   NO. Never.

### 🟢 21-30: Routing
21. **How to define a GET route?**
    *   `Route::get('/path', Action);`
22. **How to define a POST route?**
    *   `Route::post('/path', Action);`
23. **How to route to a Controller?**
    *   `[UserController::class, 'index']`
24. **How to define a route parameter?**
    *   `/user/{id}`
25. **How to make a parameter optional?**
    *   `/user/{id?}`
26. **How to add a Regex constraint?**
    *   `->where('id', '[0-9]+')`
27. **How to name a route?**
    *   `->name('profile')`
28. **How to group routes?**
    *   `Route::prefix('admin')->group(...)`
29. **What allows PUT/DELETE in HTML forms?**
    *   `@method('PUT')` (Method Spoofing).
30. **What acts as a bridge between Route and Controller?**
    *   Middleware.

### 🟢 31-40: Controllers
31. **Command to make a controller?**
    *   `php artisan make:controller Name`
32. **Command to make a Resource Controller?**
    *   `php artisan make:controller Name --resource`
33. **How to access Request data?**
    *   Injected `Request $request` object.
34. **How to get all input data?**
    *   `$request->all()`
35. **How to get one input field?**
    *   `$request->input('name')`
36. **How to check if input exists?**
    *   `$request->has('name')`
37. **How to validate input?**
    *   `$request->validate([])`
38. **How to return a View?**
    *   `return view('name');`
39. **How to return JSON?**
    *   `return response()->json([]);`
40. **How to redirect back?**
    *   `return back();`

### 🟢 41-50: Blade
41. **File extension for Blade?**
    *   `.blade.php`
42. **Echo checks for XSS?**
    *   `{{ $var }}` (Yes).
43. **Echo raw HTML?**
    *   `{!! $var !!}` (No).
44. **If statement?**
    *   `@if(...) @endif`
45. **Loop?**
    *   `@foreach(...) @endforeach`
46. **Include another view?**
    *   `@include('name')`
47. **Extend layout?**
    *   `@extends('layout')`
48. **Define content block?**
    *   `@section('content')`
49. **Check authentication?**
    *   `@auth`
50. **Display validation errors?**
    *   `@error('field')`

### 🟢 51-60: Database & Eloquent
51. **Command to create migration?**
    *   `php artisan make:migration create_users_table`
52. **Command to run migrations?**
    *   `php artisan migrate`
53. **Command to rollback migration?**
    *   `php artisan migrate:rollback`
54. **What is a Model?**
    *   Represents a database table.
55. **Retrieve all records?**
    *   `User::all()`
56. **Find by ID?**
    *   `User::find(1)`
57. **Find or fail (404)?**
    *   `User::findOrFail(1)`
58. **Filter resource?**
    *   `User::where('active', 1)->get()`
59. **Create new record?**
    *   `User::create([])`
60. **Update record?**
    *   `$user->update([])`

### 🟢 61-70: Artisan Commands
61. **List all routes?**
    *   `php artisan route:list`
62. **Clear cache?**
    *   `php artisan cache:clear`
63. **Create middleware?**
    *   `php artisan make:middleware Name`
64. **Create request form?**
    *   `php artisan make:request Name`
65. **Run tests?**
    *   `php artisan test`
66. **Enter REPL (Interactive Shell)?**
    *   `php artisan tinker`
67. **Optimize class loader?**
    *   `composer dump-autoload`
68. **Publish vendor files?**
    *   `php artisan vendor:publish`
69. **Create custom command?**
    *   `php artisan make:command Name`
70. **Key generate?**
    *   `php artisan key:generate`

### 🟢 71-80: Relationships (Basic)
71. **User has one Profile?**
    *   `hasOne`
72. **Profile belongs to User?**
    *   `belongsTo`
73. **User has many Posts?**
    *   `hasMany`
74. **Get user's posts?**
    *   `$user->posts`
75. **Get post's author?**
    *   `$post->user`
76. **What is the inverse of hasMany?**
    *   `belongsTo`
77. **What key does belongsTo look for?**
    *   `user_id` (foreign key on current table).
78. **What do you need for Many-to-Many?**
    *   A pivot table.
79. **How to define Many-to-Many?**
    *   `belongsToMany`
80. **How to count related models?**
    *   `withCount('posts')`

### 🟢 81-90: Auth
81. **Command to scaffold auth?**
    *   `composer require laravel/breeze --dev && php artisan breeze:install`
82. **Get current user?**
    *   `auth()->user()`
83. **Get user ID?**
    *   `auth()->id()`
84. **Check if logged in?**
    *   `auth()->check()`
85. **Check if guest?**
    *   `auth()->guest()`
86. **Manually login?**
    *   `Auth::login($user)`
87. **Manually logout?**
    *   `Auth::logout()`
88. **Hash a password?**
    *   `Hash::make('secret')`
89. **Protect a route?**
    *   `->middleware('auth')`
90. **Redirect unauthenticated users?**
    *   Done by `Authenticate` middleware (to `/login`).

### 🟢 91-100: Miscellaneous
91. **What is Middleware?**
    *   Code that runs before/after a request.
92. **What is CSRF?**
    *   Cross-Site Request Forgery.
93. **What is Validation?**
    *   Checking input data against rules.
94. **What is Mass Assignment?**
    *   Passing an array to `create()`/`update()`.
95. **What protects Mass Assignment?**
    *   `$fillable` or `$guarded`.
96. **What is a Seeder?**
    *   Fills DB with data.
97. **What is a Factory?**
    *   Generates fake data.
98. **What is Carbon?**
    *   PHP Date extension used by Laravel.
99. **How to format date?**
    *   `$date->format('Y-m-d')`
100. **What is Composer?**
    *   PHP Dependency Manager.
