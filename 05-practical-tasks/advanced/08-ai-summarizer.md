# 🩸 Advanced Task: AI Article Summarizer (Queue + Streaming)

### 📌 Problem Description
**"User clicks 'Summarize'. It takes 10s. Don't freeze the page."**
Use `laravel/ai` SDK.

**Requirements**:
1.  **Agent**: `SummarizerAgent`.
2.  **Job**: `SummarizeArticleJob`.
3.  **Broadcasting**: Use `Reverb` to update frontend.
4.  **UX**: Show "Summarizing..." -> Real-time stream (Bonus).

---

### 💻 Solution (The Agent)

```bash
php artisan make:agent SummarizerAgent
```

```php
// App/Ai/Agents/SummarizerAgent.php
class SummarizerAgent implements Agent
{
    use Promptable;

    public function instructions(): string {
        return "Summarize the following article in 3 bullet points. Be concise.";
    }
}
```

### 💻 Solution (The Job)

```php
// App/Jobs/SummarizeArticleJob.php
public function handle()
{
    $summary = (new SummarizerAgent)->prompt($this->post->content);
    
    $this->post->update(['summary' => $summary->text]);
    
    broadcast(new ArticleSummarized($this->post));
}
```

### 💻 Solution (The Controller - Streaming Option)

If you want **real-time typing effect** (no job):

```php
Route::get('/posts/{post}/summarize', function (Post $post) {
    return (new SummarizerAgent)
        ->stream("Summarize: " . $post->content)
        ->usingVercelDataProtocol(); // For React/Vue useVercelChat
});
```

---

### 🧪 Testing
```php
Ai::fake([
    SummarizerAgent::class => 'This is a fake summary.',
]);

$this->post('/posts/1/summarize');
// Assert job dispatched...
```
