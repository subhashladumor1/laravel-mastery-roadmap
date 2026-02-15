# 🧪 03 - Browser Testing with Laravel Dusk

### 🧐 Explanation
Most tests run **API/Controller** logic (PHP only).
What about:
1.  **Javascript logic**? (React/AlpineJS toggles)
2.  **CSS visibility**?
3.  **Complex workflows** (Drag and Drop)?

**Laravel Dusk** controls a real Chrome browser. It clicks, types, and asserts "I see 'Success'".

---

### 💻 Code Example

**❌ Bad (Skipping JS Tests):**
You assume the "Add to Cart" button works because the API works. But the frontend JS might be broken!

**✅ Dusk Test:**

```php
public function test_it_adds_product_to_cart(): void
{
    $product = Product::factory()->create(['name' => 'iPhone 15']);

    $this->browse(function (Browser $browser) use ($product) {
        $browser->visit('/products/' . $product->slug)
                ->assertSee('iPhone 15')
                ->waitFor('.add-to-cart-btn') // Use CSS selector
                ->click('.add-to-cart-btn')
                ->pause(500) // Wait for AJAX animation
                ->assertSee('Product added to cart!')
                ->assertPathIs('/cart');
    });
}
```

---

### ✅ When to Use
1.  **Critical Flows**: Checkout, Login, Admin Dashboard.
2.  **FrontendHeavy Apps**: Livewire/Vue apps where significant logic is Client-Side.

### ❌ When NOT to Use
1.  **Every Test**: Dusk is SLOW (1-2 seconds per test). Run standard HTTP tests for 95% of coverage. Run Dusk only for critical user journeys.
2.  **API Only**: If your app is just an API, Dusk is useless.

---

### 🎙 Interview Insight
**Q: "How do you run Dusk in CI/CD (GitHub Actions)?"**
**A:** "It's tricky because CI servers don't have a screen (Headless).
We must run Chrome in **headless mode** (`--headless=new`).
Also, database migration is key. Dusk runs on a separate `.env`. We usually use a separate `test_dusk` database to avoid wiping the main test database during parallel execution."
