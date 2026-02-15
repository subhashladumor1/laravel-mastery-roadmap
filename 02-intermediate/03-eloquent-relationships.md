# 🪪 03 - Polymorphic Many-to-Many Relationships

### 🧐 Explanation
You want to attach **Tags** to both **Posts** and **Videos**.
You *could* make two pivot tables: `post_tags` and `video_tags`.
Or you can make **ONE** pivot table: `taggables`.

This uses `taggable_id` and `taggable_type` columns.

---

### 💻 Database Schema

```php
Schema::create('taggables', function (Blueprint $table) {
    $table->foreignId('tag_id')->constrained()->cascadeOnDelete();
    
    // Creates `taggable_id` (bigint) and `taggable_type` (string)
    $table->morphs('taggable'); 
    
    $table->unique(['tag_id', 'taggable_id', 'taggable_type']);
});
```

---

### 💻 Models

**Model:** `app/Models/Post.php`

```php
public function tags()
{
    // 'taggable' is the prefix of the generic columns
    return $this->morphToMany(Tag::class, 'taggable');
}
```

**Model:** `app/Models/Video.php`

```php
public function tags()
{
    return $this->morphToMany(Tag::class, 'taggable');
}
```

**Model:** `app/Models/Tag.php`

```php
public function posts()
{
    return $this->morphedByMany(Post::class, 'taggable');
}

public function videos()
{
    return $this->morphedByMany(Video::class, 'taggable');
}
```

---

### ✅ Using It

```php
// Attaching Tags
$post->tags()->attach([1, 2]); // Only works for Post
$video->tags()->sync([2, 3]); // Automatically sets taggable_type = 'App\Models\Video'

// Querying All Content with Tag #1
$posts = Tag::find(1)->posts;
$videos = Tag::find(1)->videos;

// Getting All Content Count
$count = Tag::withCount(['posts', 'videos'])->get();
```

---

### 🎙 Interview Insight
**Q: "Why define a Morph Map instead of storing full class names in the database?"**

**A:**
"Storing `App\Models\Post` in your database couples your data to your PHP namespace structure. If you refactor `Post` to `Article`, your database breaks.
In `AppServiceProvider::boot()`, call `Relation::enforceMorphMap(['post' => Post::class])`. 
This stores the string `'post'` in the database instead of the full class path. It decouples the data structure from the application structure."
