# 🧪 02 - Factories & Seeders: Test Data on Steroids

### 🧐 Explanation
Before Laravel, we manually wrote SQL inserts for test data.
**Factories** generate dummy data (`User::factory()`).
**Seeders** use Factories to fill the database (`php artisan db:seed`).

**Goal**: Zero config `php artisan migrate:fresh --seed`.
Every developer should be able to run the app in **10 seconds**.

---

### 💻 Code Example

**❌ Bad (Manual Creation in Test):**

```php
public function test_it_shows_posts()
{
    // Painful
    $user = User::create(['name' => 'Alice', 'email' => 'foo@bar.com', 'password' => 'secret']);
    $post = Post::create(['title' => 'Title', 'body' => 'Lorem', 'user_id' => $user->id]);
    
    $this->get('/posts')->assertSee('Title');
}
```

**✅ Good (Factory + States):**

**Model Factory:** `database/factories/PostFactory.php`

```php
public function definition(): array
{
    return [
        'title' => fake()->sentence(),
        'body' => fake()->paragraph(),
        'user_id' => User::factory(), // Magic! Creates user automatically.
        'status' => 'draft',
    ];
}

public function published(): Factory
{
    return $this->state(['status' => 'published']);
}
```

**Test:**

```php
it('shows published posts only', function () {
    // 1 line creates: 1 User, 3 Published Posts, 2 Draft Posts
    $published = Post::factory()->count(3)->published()->create();
    $draft = Post::factory()->count(2)->create();
    
    // Act
    $response = get('/posts');
    
    // Assert
    $response->assertSee($published->first()->title);
    $response->assertDontSee($draft->first()->title);
});
```

---

### ✅ When to Use
1.  **Always**: Use factories for Models.
2.  **States**: Define states (`admin`, `banned`, `verified`) to keep tests readable.

### ❌ When NOT to Use
1.  **Production Data**: Don't use Faker in production.

---

### 🎙 Interview Insight
**Q: "What is `RefreshDatabase` trait?"**
**A:** "It wraps every test in a Database Transaction.
Before the test starts: Transaction BEGIN.
After the test ends: Transaction ROLLBACK.
This is incredibly fast because it never actually writes to disk, ensuring a clean slate for every test case."
