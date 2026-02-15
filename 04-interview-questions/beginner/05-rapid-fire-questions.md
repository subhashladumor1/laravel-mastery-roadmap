# ⚡ 50+ Beginner Laravel Interview Questions (Rapid Fire)

> **Instructions**: These are "Yes/No" or "One Sentence" answers. Great for quick revision before an interview.

---

### 🟢 General & Artisan
1.  **What is the command to start the development server?**
    *   `php artisan serve`
2.  **How do you create a controller named `UserController`?**
    *   `php artisan make:controller UserController`
3.  **What does `php artisan migrate` do?**
    *   Runs pending database migrations.
4.  **Where is the detailed configuration for database connection?**
    *   `.env` file (and `config/database.php`).
5.  **What is the default templating engine in Laravel?**
    *   Blade.
6.  **How do you clear the application cache?**
    *   `php artisan cache:clear`
7.  **What command creates a Model, Controller, Migration, and Factory all at once?**
    *   `php artisan make:model Post -mcrf` (or `-a` for all).
8.  **Where are routes defined for a web application?**
    *   `routes/web.php`
9.  **Where are routes defined for an API?**
    *   `routes/api.php`
10. **What is Composer?**
    *   A dependency manager for PHP.

---

### 🟢 Routing & Controllers
11. **How do you define a route that accepts an ID?**
    *   `Route::get('/user/{id}', ...)`
12. **How do you name a route?**
    *   `Route::get(...)->name('profile');`
13. **How do you generate a URL for a named route?**
    *   `route('profile')`
14. **What is a Resource Controller?**
    *   A controller with pre-defined methods for CRUD (index, create, store, show, edit, update, destroy).
15. **How do you list all registered routes?**
    *   `php artisan route:list`
16. **What is the function to redirect the user?**
    *   `return redirect('/home');`
17. **How do you access query parameters?**
    *   `$request->query('param')` or `$request->input('param')`.
18. **How do you validate a request in a controller?**
    *   `$request->validate(['field' => 'required']);`
19. **What stops CSRF attacks in Laravel forms?**
    *   The `@csrf` blade directive.
20. **What HTTP verb is used for updating data?**
    *   PUT or PATCH.

---

### 🟢 Blade Templating
21. **How do you output a variable in Blade?**
    *   `{{ $variable }}`
22. **check if a user is authenticated in Blade?**
    *   `@auth ... @endauth`
23. **How do you include a sub-view?**
    *   `@include('partials.header')`
24. **How do you extend a layout?**
    *   `@extends('layouts.app')`
25. **How do you define a section content?**
    *   `@section('content') ... @endsection`
26. **What is the difference between `{{ $a }}` and `{!! $a !!}`?**
    *   `{{ }}` escapes HTML (secure). `{!! !!}` renders raw HTML (dangerous).
27. **How do you write an API loop in Blade?**
    *   `@foreach($users as $user) ... @endforeach`
28. **How do you verify if a section exists?**
    *   `@hasSection('title')`
29. **What variable is available in every view to check errors?**
    *   `$errors`
30. **How do you create a comment in Blade?**
    *   `{{-- This is a comment --}}`

---

### 🟢 Eloquent & Database
31. **What is Eloquent?**
    *   Laravel's Object-Relational Mapper (ORM).
32. **What determines the table name for a model named `Product`?**
    *   Snake case plural: `products`.
33. **How do you retrieve all rows from a table model?**
    *   `Product::all()`
34. **How do you find a record by Primary Key?**
    *   `Product::find(1)`
35. **What happens if `Product::findOrFail(1)` finds nothing?**
    *   Throws `ModelNotFoundException` (404 Page).
36. **How do you define a One-to-One relationship?**
    *   `public function phone() { return $this->hasOne(Phone::class); }`
37. **How do you define the inverse of One-to-One?**
    *   `belongsTo(User::class)`
38. **What does `fillable` property do?**
    *   Allows Mass Assignment for specific columns.
39. **What does `guarded` property do?**
    *   Blacklists columns from Mass Assignment (Opposite of fillable).
40. **How do you enable soft deletes?**
    *   Use `SoftDeletes` trait and add `deleted_at` column.
41. **How do you get the ID of the last inserted record?**
    *   `$model->id` (after calling save/create).
42. **What format does Laravel store timestamps in?**
    *   `Y-m-d H:i:s` (Carbon instance).
43. **How do you run a raw SQL query?**
    *   `DB::select('SELECT * FROM users');`
44. **What is a Seeder?**
    *   A class to populate database with test/dummy data.
45. **What is a Factory?**
    *   A class to generate fake data for models using Faker.

---

### 🟢 Auth & Security
46. **How do you get the currently logged-in user?**
    *   `Auth::user()` or `auth()->user()`.
47. **How do you get the ID of the logged-in user?**
    *   `Auth::id()`.
48. **How do you verify a password manually?**
    *   `Hash::check('text', $hashedPassword)`.
49. **Where are passwords hashed?**
    *   In the database (using Bcrypt/Argon2).
50. **What middleware protects routes from unauthenticated users?**
    *   `auth` middleware.
