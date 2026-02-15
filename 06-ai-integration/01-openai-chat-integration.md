# 🤖 01 - Intelligent OpenAI Integration (Service Pattern)

### 🧐 Explanation
Integrating OpenAI in Laravel isn't just about calling `Http::post`. For production systems, you need a robust Service Pattern that handles:
1.  **Retry logic** (OpenAI fails unpredictably).
2.  **Rate limiting**.
3.  **Cost monitoring** (Token usage).
4.  **Logging** (Audit trail).

### 💻 Code Example

**File:** `app/Services/AI/OpenAIService.php`

```php
<?php

namespace App\Services\AI;

use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Log;
use Illuminate\Http\Client\Pool;

class OpenAIService
{
    protected string $apiKey;
    protected string $baseUrl = 'https://api.openai.com/v1';

    public function __construct()
    {
        $this->apiKey = config('services.openai.key');
    }

    /**
     * Generate a completion with automatic retries and logging.
     */
    public function complete(string $prompt, string $model = 'gpt-4o-mini'): string
    {
        // 1. Logging Input (Sanitize sensitive data in real apps!)
        Log::info("AI Request: [{$model}] " . substr($prompt, 0, 50) . "...");

        // 2. HTTP Client with Retry logic
        $response = Http::withToken($this->apiKey)
            ->timeout(30)
            ->retry(3, 100, function ($exception, $request) {
                return $exception instanceof \Illuminate\Http\Client\ConnectionException;
            })
            ->post("{$this->baseUrl}/chat/completions", [
                'model' => $model,
                'messages' => [
                    ['role' => 'system', 'content' => 'You are a helpful assistant.'],
                    ['role' => 'user', 'content' => $prompt],
                ],
                'temperature' => 0.7,
            ]);

        if ($response->failed()) {
            Log::error("AI Error: " . $response->body());
            throw new \Exception("AI Service Unavailable.");
        }

        $content = $response->json('choices.0.message.content');
        
        // 3. Token usage tracking (Store in DB for billing)
        $usage = $response->json('usage');
        // UsageService::track($usage['total_tokens']);

        return $content;
    }
}
```

**File:** `app/Http/Controllers/BlogController.php`

```php
public function generateSummary(Post $post, OpenAIService $ai)
{
    // Always use Queues for AI calls!
    GenerateSummaryJob::dispatch($post); 
    
    return back()->with('status', 'Summary is being generated...');
}
```

---

### ✅ When to Use
1.  **Content Generation**: Blog outlines, SEO descriptions, email drafts.
2.  **Data Extraction**: Extracting JSON data from unstructured text (emails/PDFs).
3.  **Chatbots**: Customer support interaction (with RAG).
4.  **Code Analysis**: PR reviews (AI code review bot).

### ❌ When NOT to Use
1.  **Deterministic Logic**: Don't ask AI to calculate `15 * 45`. It hallucinates. Use standard code.
2.  **Sensitive PII**: Never send User Passwords, SSNs, or Credit Cards to OpenAI unless you have a strict BAA/Enterprise agreement.
3.  **High-Speed Realtime**: Pure HTTP calls add 2-10 seconds latency. Not for "instant" UI feedback without streaming.

---

### 🎙 Interview Insight

**Q: How do you handle OpenAI API failures and timeouts in a high-traffic Laravel application?**

**A (Senior Answer):**
"Directly calling the API in the Controller is a mistake because it blocks the PHP-FPM worker. I process all AI requests via **Queues (Horizon)** with a `timeout` set higher than the OpenAI response time (e.g., 120s). 
I implement a **Service Class** wrapper using Laravel's HTTP client `retry()` method to handle transient network errors (429/500). 
Crucially, for cost control, I log token usage per tenant/user and implement a localized **Cache Rate Limiter** tailored to our budget, not just the API's global limit."

---
