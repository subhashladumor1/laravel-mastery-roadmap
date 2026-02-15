# 🍼 02 - Clean Controllers: Putting Logic on a Diet

### 🧐 Explanation
**Golden Rule**: Controllers should **coordinate**, not **calculate**.
Think of a Controller as a "Traffic Cop".
1.  Receive Request (`$request->all()`).
2.  Route to Service (`UserService->register()`).
3.  Return Response (`view('home')`).

If you see `Mail::send(...)` or `DB::transaction(...)` in your controller, you are doing it wrong.

---

### 💻 Code Example

**❌ Bad (Everything in Controller):**

```php
public function store(Request $request)
{
    // 1. Validation Logic
    if (!$request->has('email')) {
        abort(400);
    }
    
    // 2. Business Logic
    $user = User::create($request->all());
    
    // 3. Side Effects
    Mail::to($user)->send(new WelcomeEmail());
    
    return back();
}
```

**✅ Good (Clean & Single Action):**

**Form Request**: `app/Http/Requests/StoreUserRequest.php`

```php
public function rules() 
{
    return ['email' => 'required|email'];
}
```

**Action**: `app/Actions/CreateUserAction.php`

```php
class CreateUserAction 
{
    public function execute(array $data) 
    {
        $user = User::create($data);
        event(new UserRegistered($user));
        return $user;
    }
}
```

**Controller**: `app/Http/Controllers/UserController.php`

```php
use App\Http\Requests\StoreUserRequest;
use App\Actions\CreateUserAction;

public function store(StoreUserRequest $request, CreateUserAction $action)
{
    $action->execute($request->validated());

    return to_route('dashboard')->with('success', 'User created!');
}
```

---

### ✅ When to Use
1.  **Always**: Keep controllers under 50 lines.
2.  **Strict Typing**: Type-hint your Actions and Requests.

### ❌ When NOT to Use
1.  **Never**: There is no excuse for fat controllers in 2024.

---

### 🎙 Interview Insight
**Q: "Why are Single Action Controllers (__invoke) popular?"**
**A:** "They force the Single Responsibility Principle (SRP). Instead of a `UserController` with 7 methods (index, store, update...), you create `StoreUserController`, `UpdateUserController`.
This makes routing explicit (`Route::post('/users', StoreUserController::class)`) and prevents massive 2000-line controller files."
