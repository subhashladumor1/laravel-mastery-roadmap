# 🧠 02 - Laravel Pipelines: The Chain of Responsibility

### 🧐 Explanation
Imagine a "Search Request" that needs:
1.  **Filtering** (Active users only)
2.  **Sorting** (By name)
3.  **Pagination** (Limit 15)

You *could* write 3 `if` statements in your controller.
Or you can write a **Pipeline**.

Laravel uses Pipelines internally for **Middleware**.
You can use them for **complex workflows** (Order processing, Content filtering).

---

### 💻 Code Example

**Service:** `app/Services/Search/PostSearch.php`

```php
use Illuminate\Pipeline\Pipeline;
use App\Models\Post;

// Filters
class Active
{
    public function handle($content, Closure $next)
    {
        if (request()->has('active')) {
            $content->where('active', 1);
        }
        return $next($content);
    }
}

class Sort
{
    public function handle($content, Closure $next)
    {
        if (request()->has('sort')) {
            $content->orderBy('created_at', request('sort'));
        }
        return $next($content);
    }
}

// Pipeline Executor (Controller)
public function index()
{
    $posts = app(Pipeline::class)
        ->send(Post::query()) // Start with a query builder
        ->through([
            Active::class,
            Sort::class,
            // Add as many filters as you want dynamically!
        ])
        ->thenReturn()
        ->get(); // Final execution

    return view('posts.index', compact('posts'));
}
```

---

### ✅ When to Use
1.  **Complex Searching**: Build dynamic queries based on 20 filters.
2.  **Order Fulfillment**: Check stock -> Charge -> Email -> Ship. Each step is a pipe.
3.  **Middleware**: Implementing your own middleware logic outside HTTP.

### ❌ When NOT to Use
1.  **Simple logic**: Don't use a pipeline for `->where('id', 1)`. That's overkill.
2.  **Coupled Steps**: If step 3 depends heavily on step 1's side effects, a pipeline might hide the dependency. Use explicit procedural code.

---

### 🎙 Interview Insight
**Q: "What pattern does Laravel Pipeline implement?"**
**A:** "It implements the **Chain of Responsibility** pattern (similar to UNIX pipes `|`).
Each pipe receives a pass-through object (`$content`) and a closure (`$next`). It can modify the object and pass it forward OR stop the chain (like Middleware creating a redirect)."
