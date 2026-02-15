# 🍼 Intermediate Task: Shopping Cart

### 📌 Problem Description
**"Create a session-based Shopping Cart (Guest)."**
User visits `/products`. Clicks "Add to Cart".
Wants to see Cart content later.

**Requirements**:
1.  **Add**: POST `/cart/add`.
2.  **View**: GET `/cart`.
3.  **Update**: PATCH `/cart/item/1` (Quantity).
4.  **Remove**: DELETE `/cart/item/1`.
5.  **Total**: Calculate subtotal dynamically.

---

### 💻 Solution (Service)

**CartService.php**

```php
public function add(Product $product, int $quantity = 1)
{
    $cart = session()->get('cart', []);
    
    if(isset($cart[$product->id])) {
        $cart[$product->id]['quantity'] += $quantity;
    } else {
        $cart[$product->id] = [
            "name" => $product->name,
            "quantity" => $quantity,
            "price" => $product->price,
        ];
    }
    
    session()->put('cart', $cart);
}
```

---

### 🧪 Test It (Pest)
`test('guest can add item to cart')` -> assertSessionHas('cart').
