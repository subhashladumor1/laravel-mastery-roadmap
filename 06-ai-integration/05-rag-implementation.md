# 🤖 05 - RAG (Retrieval Augmented Generation)

### 🧐 Explanation
**RAG** = **Search** + **AI**.
ChatGPT doesn't know about *your* private company data.
But you can't re-train models every day (too expensive).

So we provide the "context" inside the prompt.
1.  **User Asks**: "How much did we earn in Q3?"
2.  **Laravel App**: Queries 'Q3 Revenue Report', gets text chunks.
3.  **Prompt**: "Here is the revenue data: $50,000. Answer the user's question."
4.  **AI**: "You earned $50,000 in Q3."

---

### 💻 Step 1: Chunking & Embedding

**Job:** `app/Jobs/ProcessDocumentJob.php`

```php
// 1. Read PDF/Document
$text = PdfReader::getText($path);

// 2. Chunk (Split into 500-token pieces)
$chunks = str_split_chunk($text, 2000); // Helper function

foreach ($chunks as $chunk) {
    // 3. Generate Embedding
    $vector = OpenAI::embeddings()->create([
        'input' => $chunk,
        'model' => 'text-embedding-3-small',
    ])->embeddings[0]->embedding;

    // 4. Save to Database
    DocumentChunk::create([
        'content' => $chunk,
        'embedding' => $vector,
    ]);
}
```

---

### 💻 Step 2: Querying with Context

**Service:** `app/Services/AI/RagService.php`

```php
public function answerQuestion(string $question)
{
    // 1. Embed the Question
    $questionVector = $this->embed($question);

    // 2. Find closest Context (Vector Search)
    $contexts = DocumentChunk::orderByRaw("embedding <=> '{$questionVector}'")
        ->limit(3)
        ->pluck('content')
        ->implode("\n\n");

    // 3. Construct Prompt
    $prompt = "Use the following context to answer the question.
    Context: {$contexts}
    
    Question: {$question}";

    // 4. Ask OpenAI
    return OpenAI::chat()->create([
        'model' => 'gpt-4o',
        'messages' => [['role' => 'user', 'content' => $prompt]],
    ])->choices[0]->message->content;
}
```

---

### ✅ When to Use
1.  **Knowledge Base Chatbots**: Customer support relying on strict manuals.
2.  **Private Analytics**: Asking questions about proprietary data without sending the data for training.
3.  **Legal Document Analysis**: "Summarize clause 5.2 from this contract."

### ❌ When NOT to Use
1.  **General Chat**: If the AI already knows the answer (e.g., "What is PHP?"), RAG is wasted effort.
2.  **Too Large Context**: If your context exceeds the model's token limit (e.g., trying to feed an entire book into GPT-3.5), it will fail or be expensive.

---

### 🎙 Interview Insight
**Q: "How do you handle context window limits in RAG?"**
**A:** "We use a 'sliding window' approach or summarize previous turns. 
More importantly, we optimize the retrieval step (the 'R' in RAG). Instead of just taking the top 5 chunks, we use **Re-ranking** (using a Cross-Encoder like Cohere) to ensure the retrieved context is genuinely relevant before consuming expensive GPT-4 tokens."
