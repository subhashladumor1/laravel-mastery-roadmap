# 💰 06 - AI Cost Management: Stopping the Bleeding

### 🧐 Explanation
OpenAI bills by **Token**.
*   **Input Tokens**: Cheap ($0.15/1M).
*   **Output Tokens**: Expensive ($0.60/1M).
*   **Vector Search**: Storage cost + embedding cost.

**Problem**: One loop bug = $500 overnight.

---

### 💻 Strategy 1: Token Counting (tiktoken)

Before sending *any* request, count tokens locally. **Reject long inputs**.

**Package**: `composer require tiktoken-php/tiktoken-php`

**Middleware:** `app/Http/Middleware/CheckTokenLimit.php`

```php
use TikToken\Encoder;

public function handle($request, Closure $next)
{
    $encoder = Encoder::get('o200k_base'); // GPT-4o tokenizer
    $tokens = count($encoder->encode($request->input('prompt')));

    if ($tokens > 2000) {
        return response()->json(['error' => 'Prompt too long! Max 2000 tokens.'], 400);
    }
    
    // Store estimated cost in DB
    RequestLog::create(['estimated_cost' => $tokens * 0.000005]);

    return $next($request);
}
```

---

### 💻 Strategy 2: Caching (Redis)

If 100 users ask "What is loop in PHP?", don't pay OpenAI 100 times.
Cache the **Vector of the question**.

**Service:** `app/Services/AI/SmartCacheService.php`

```php
public function getAnswer(string $question)
{
    // 1. Check exact string match (Redis)
    $cacheKey = 'ai_q_' . md5($question);
    if (Cache::has($cacheKey)) {
        return Cache::get($cacheKey);
    }

    // 2. Check semantic match (Vector DB)
    // If a very similar question was asked 1 min ago...
    $similar = Question::whereVector('embedding', $this->embed($question))
        ->where('distance', '<', 0.1) // 90% match
        ->first();

    if ($similar) {
        return $similar->answer;
    }

    // 3. Call OpenAI (Expensive)
    $answer = OpenAI::chat()->create(...)->content;
    
    // 4. Cache it
    Cache::put($cacheKey, $answer, 3600);
    
    return $answer;
}
```

---

### ✅ When to Use
1.  **Public Chatbots**: Users will spam "Hello". Cache it.
2.  **Internal Tools**: Rate limits per team/user to prevent abuse.
3.  **Testing**: Mock OpenAI in `phpunit.xml` to avoid billing during CI/CD.

### ❌ When NOT to Use
1.  **Unique Content Generation**: Every bio/email is different. Caching won't help.
2.  **Streaming**: Difficult to cache streams partially.

---

### 🎙 Interview Insight
**Q: "How do you prevent a user from bankrupting your SaaS with API calls?"**
**A:** "I implement a **Token Budget System**.
Each user has a daily allowance (e.g., 100k tokens). I track usage in Redis (atomic increment). Before processing any job, I check `Redis::get('user:1:daily_tokens') < LIMIT`.
Additionally, I set a `timeout` on HTTP requests and use `hard_limit` configurations in the OpenAI client to prevent runaway loops."
