# 📦 04 - Excel Import: Handling Massive CSVs

### 🧐 Explanation
"Upload a CSV (2MB)".
**Old Way**:
1.  Increase `post_max_size`.
2.  `foreach ($file as $line)`.
3.  Timeout after 30 seconds.
4.  Crash server (Memory Limit).

**New Way (Maatwebsite)**:
1.  Read in Chunks (1000 rows).
2.  Queue in Background (Job).
3.  Batch Insert (Eloquent).

---

### 💻 Setup

```bash
composer require maatwebsite/excel
php artisan make:import UsersImport --model=User
```

---

### 💻 Code Example

**❌ Bad (Sync Import):**

```php
Excel::import(new UsersImport, $file); // Blocks UI for 5 minutes!
```

**✅ Good (Async Chunked):**

**Class**: `app/Imports/UsersImport.php`

```php
use Maatwebsite\Excel\Concerns\ToModel;
use Maatwebsite\Excel\Concerns\WithChunkReading;
use Maatwebsite\Excel\Concerns\WithBatchInserts;
use Illuminate\Contracts\Queue\ShouldQueue;

class UsersImport implements ToModel, WithChunkReading, WithBatchInserts, ShouldQueue
{
    public function model(array $row)
    {
        return new User([
            'name'     => $row[0],
            'email'    => $row[1],
            'password' => Hash::make($row[2]),
        ]);
    }

    public function chunkSize(): int
    {
        return 1000; // Read 1000 rows at a time
    }
    
    public function batchSize(): int
    {
        return 1000; // Insert 1000 rows per SQL query (INSERT INTO users VALUES (...) ...)
    }
}
```

**Controller**:

```php
Excel::queueImport(new UsersImport, $file); // Returns immediately!
```

---

### ✅ When to Use
1.  **Always**: Use `queueImport` for > 1000 rows.
2.  **Validation**: Implement `WithValidation` interface to validate rows before import logic.

### ❌ When NOT to Use
1.  **Read-Only**: If you just need to parse CSV text into array, avoid this package overhead. Use native `str_getcsv`.
2.  **Insane Scale**: Keep in mind Excel is slow. If importing 10M rows, use `LOAD DATA INFILE` (MySQL Raw) via CLI.

---

### 🎙 Interview Insight
**Q: "Why chunk read *and* batch insert?"**
**A:** "Chunk Reading keeps RAM usage low (Processing 1000 objects in memory instead of 1,000,000).
Batch Inserting minimizes SQL queries (1 INSERT query for 1000 rows instead of 1000 queries).
Together, they solve the Memory Limit and Timeout issues respectively."
