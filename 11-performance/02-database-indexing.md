# ⚡ 02 - Database Indexing (Stop Sequential Scans)

### 🧐 Explanation
Imagine looking for "John" in a phone book.
**Without Index (Full Table Scan)**: You read every name from A to Z. O(N).
**With Index (B-Tree)**: You jump to J. Then Jo. Then Joh. O(log N).

An index is a sorted data structure (usually B-Tree) that points to the data row.

---

### 💻 Code Example

**❌ Bad (Missing Index):**

```php
// Migration
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('email'); // No index!
    $table->string('status');
});

// Query: SELECT * FROM users WHERE email = 'foo@bar.com';
// Scans 1,000,000 rows. Takes 2 seconds.
```

**✅ Good (Indexed):**

```php
// Migration
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('email')->unique(); // Creates Index!
    $table->string('status')->index(); // Creates Index!
});

// Query: SELECT * FROM users WHERE email = 'foo@bar.com';
// Scans 5 rows (Tree traversal). Takes 0.001 seconds.
```

**✅ Better (Composite Index):**

If you often filter by `status` AND `created_at`:

```php
$table->index(['status', 'created_at']);
```

---

### ✅ When to Use
1.  **Always**: Primary Keys (id), Foreign Keys (user_id).
2.  **Filter Columns**: WHERE strict equality (`status = 'active'`).
3.  **Ordering**: ORDER BY (`created_at`).

### ❌ When NOT to Use
1.  **Low Cardinality**: Columns with few unique values (e.g., `gender`: 'M', 'F'). Index scan is slower than table scan here.
2.  **Write-Heavy Tables**: Every INSERT updates the index tree. Too many indexes slow down writes.

---

### 🎙 Interview Insight
**Q: "What is `EXPLAIN ANALYZE` in MySQL/Postgres?"**
**A:** "It tells you the query execution plan.
If you see `type: ALL` (Full Table Scan), your query is slow and needs an index.
If you see `type: ref` or `range` (Index Scan), that's good.
Always run `EXPLAIN` before deploying a complex query to production."
