# 🧪 01 - Pest vs PHPUnit in Laravel

### 🧐 Explanation
Every Laravel project includes PHPUnit.
**Pest PHP** is a wrapper around PHPUnit.
It has **Cleaner Syntax**, **Datasets**, **Hooks**, and **Expectations**.

**Goal**: Make tests readable by everyone (even Product Managers!).

---

### 💻 Code Example

**❌ Failed PHPUnit (Obscure):**

```php
// UserTest.php
public function test_it_archives_a_user()
{
    $user = User::factory()->create();
    
    $this->post("/users/{$user->id}/archive")
        ->assertRedirect('/users');
        
    $this->assertTrue($user->fresh()->is_archived);
}
```

**✅ Pest (Readable):**

```php
// UserTest.php

it('archives a user successfully', function () {
    // Arrange
    $user = User::factory()->create();
    
    // Act
    post(route('users.archive', $user))
        ->assertRedirect(route('users.index'));
        
    // Assert
    expect($user->fresh()->is_archived)->toBeTrue();
});
```

---

### 💻 Features

1.  **Datasets**: Run test against multiple values.

```php
it('validates email', function ($email) {
    post('/login', ['email' => $email])->assertSessionHasErrors('email');
})->with([
    'invalid@', 
    'no-domain', 
    'foo.com'
]);
```

2.  **Higher Order Tests**: Even shorter!

```php
it('shows home page')->get('/')->assertStatus(200);
```

---

### ✅ When to Use
1.  **New Projects**: Use Pest. `laravel new my-app --pest`.
2.  **Existing Projects**: Migrate gradually (`pest-plugin-drift` converts PHPUnit -> Pest).

### ❌ When NOT to Use
1.  **Enterprise Legacy**: Deep inheritance on `TestCase` might be easier to keep in PHPUnit classes.

---

### 🎙 Interview Insight
**Q: "Is Pest just syntactic sugar?"**
**A:** "Technically, yes, it compiles to PHPUnit under the hood. 
However, the **Developer Experience (DX)** improvement is massive. Readable tests are maintained tests.
Also, Pest introduces architectural testing (`arch()->preset()->laravel()`) which enforces rules like 'Controllers should not use DB facade directly'."
