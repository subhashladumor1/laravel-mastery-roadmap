# 🤖 04 - Vector Embeddings in Laravel

### 🧐 Explanation
Vectors are confusing. Don't overthink them.
**Vector = Array of floats**.
**Embedding = Convert Text to Vector.**

Imagine Google coordinates.
*   "Apple" -> `[0.2, 0.4]`
*   "Banana" -> `[0.25, 0.45]` (Close)
*   "Car" -> `[0.9, 0.9]` (Far Away)

We compute the "distance" (Cosine Similarity) between two sentences to find semantic meaning.

**Use Case**: "Find products related to 'shoes for running' without matching the exact keyword 'shoes'".

---

### 💻 pgvector Setup (PostgreSQL)

**Schema:**
```php
// Migration
Schema::create('products', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->text('description');
    
    // Install pgvector extension first!
    // CREATE EXTENSION IF NOT EXISTS vector;
    
    // Store OpenAI Embedding (1536 dimensions for text-embedding-3-small)
    $table->vector('embedding', 1536)->nullable(); 
});
```

---

### 💻 Generating Embeddings

**Service:** `app/Services/AI/EmbeddingService.php`

```php
use OpenAI\Laravel\Facades\OpenAI;

public function embed(string $text): array
{
    // Replace newlines to improve accuracy
    $text = str_replace("\n", " ", $text);
    
    $response = OpenAI::embeddings()->create([
        'model' => 'text-embedding-3-small',
        'input' => $text,
    ]);

    return $response->embeddings[0]->embedding; // [0.0123, -0.0234, ...]
}
```

---

### 💻 Searching (The Magic)

**Repository:** `app/Repositories/ProductRepository.php`

```php
// Using raw SQL for Cosine Distance (<=> operator in pgvector)
public function search(string $query, int $limit = 5)
{
    $queryVector = json_encode($this->embeddingService->embed($query));

    return Product::selectRaw("*, embedding <=> '{$queryVector}' as distance")
        ->orderBy('distance') // Closest distance = Most relevant
        ->limit($limit)
        ->get();
}
```

---

### ✅ When to Use
1.  **Semantic Search**: "Find movies about time travel" matches "Back to the Future".
2.  **Product Recommendations**: "Users who liked this also liked...".
3.  **Spam Detection**: Similar embeddings to known spam messages.

### ❌ When NOT to Use
1.  **Exact Keyword Match**: Looking for "Invoice INV-1020". Vectors are fuzzy, they might find "INV-1021". Use SQL `WHERE` for exact matches.
2.  **Simple Search**: If you just need Full Text Search, use Algolia/Meilisearch. Vectors are expensive to compute.

---

### 🎙 Interview Insight
**Q: "What are the trade-offs of storing vectors in Postgres vs a dedicated Vector DB like Pinecone/Weaviate?"**
**A:** "Postgres with `pgvector` allows for Hybrid Search (combining vector similarity + traditional SQL filters like `price < 100`). It simplifies infrastructure because my data and vectors live in one ACID-compliant database.
However, specialized Vector DBs like Pinecone scale better for billions of vectors and offer faster indexing algorithms (HNSW) out of the box for massive datasets."
