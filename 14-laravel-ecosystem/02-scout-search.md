# 🌐 02 - Scout: Instant Search (Stop Using LIKE '%foo%')

### 🧐 Explanation
`LIKE %term%` is SLOW.
It scans every row. It doesn't handle typos ("appl" -> "apple").
**Laravel Scout** pushes your data to a Search Engine:
1.  **Algolia / Meilisearch**: Fast, Relevant, Typo-tolerant.
2.  **Database**: Just `LIKE` but with clean syntax.

---

### 💻 Setup (Meilisearch - Free & Self-Hosted)

```bash
composer require laravel/scout
composer require meilisearch/meilisearch-php
php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider"
```

**Docker (Meilisearch)**:
```yaml
meilisearch:
    image: getmeili/meilisearch:latest
    ports:
        - '7700:7700'
```

---

### 💻 Code Example

**Model**: `app/Models/Post.php`

```php
use Laravel\Scout\Searchable;

class Post extends Model
{
    use Searchable;

    // Optional: Customize what gets indexed
    public function toSearchableArray()
    {
        return [
            'id' => $this->id,
            'title' => $this->title,
            'body' => strip_tags($this->body), // Don't index HTML tags!
            'category' => $this->category->name,
        ];
    }
}
```

**Controller**:

```php
// Automatically hits Meilisearch API!
$posts = Post::search('laravel tutrolial')->paginate(20); 
// Even with typo 'tutrolial', it finds 'tutorial'.
```

---

### ✅ When to Use
1.  **E-commerce**: Users search "shoes". You want relevance ranking (Best sellers first).
2.  **Blog**: Searching content body.

### ❌ When NOT to Use
1.  **Internal ID Lookup**: Searching strictly by Order ID `#12345`. Just use `where('id', 12345)`.
2.  **Simple Filtering**: "Show active users". Just use scopes.

---

### 🎙 Interview Insight
**Q: "If I update a model, is the search index updated instantly?"**
**A:** "Yes, Laravel uses Model Observers.
`saved` event -> Updates Index.
`deleted` event -> Removes from Index.
However, for bulk updates (`Post::where(..)->update(..)`), Observers do NOT fire!
You must manually call `Post::searchable()` on the collection to sync the changes."
