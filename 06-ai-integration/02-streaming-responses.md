# 🤖 02 - Streaming OpenAI Responses (Server-Sent Events)

### 🧐 Explanation
Nobody likes waiting 5 seconds for a chatbot to type.
**Streaming** allows you to show words *as they are generated* (like ChatGPT), improving Perceived Performance by 1000%.

**Tech Stack**:
*   Laravel (Backend): Output buffer streaming.
*   Javascript (Frontend): `EventSource` or `fetch` with `ReadableStream`.

---

### 💻 Code Example

**Controller:** `app/Http/Controllers/ChatController.php`

```php
use Illuminate\Http\Request;
use OpenAI\Laravel\Facades\OpenAI;

public function streamResponse(Request $request)
{
    $question = $request->input('message');

    return response()->stream(function () use ($question) {
        $stream = OpenAI::chat()->createStreamed([
            'model' => 'gpt-3.5-turbo',
            'messages' => [
                ['role' => 'user', 'content' => $question],
            ],
        ]);

        foreach ($stream as $response) {
            $text = $response->choices[0]->delta->content ?? '';
            if (connection_aborted()) {
                break;
            }
            // Format as Server-Sent Event (SSE)
            echo "data: " . json_encode(['text' => $text]) . "\n\n";
            ob_flush();
            flush();
        }
        
        echo "event: stop\n";
        echo "data: stopped\n\n";
        ob_flush();
        flush();
        
    }, 200, [
        'Cache-Control' => 'no-cache',
        'X-Accel-Buffering' => 'no', // Essential for Nginx
        'Content-Type' => 'text/event-stream',
    ]);
}
```

**Frontend (Vanilla JS):**

```javascript
const eventSource = new EventSource('/chat/stream?message=Hello');

eventSource.onmessage = function(e) {
    const data = JSON.parse(e.data);
    document.getElementById('chat-box').innerText += data.text;
};

eventSource.addEventListener('stop', function() {
    eventSource.close();
});
```

---

### ✅ When to Use
1.  **Chat Interfaces**: Mandatory.
2.  **Long Generation Tasks**: Writing blog posts, generating code.
3.  **Real-time Progress Feeds**: "Analyzing... Thinking... Writing...".

### ❌ When NOT to Use
1.  **Simple JSON APIs**: If another machine consumes your API, they usually want the full JSON at once.
2.  **Background Processing**: If users don't need to see it live, use a Queue.

---

### 🎙 Interview Insight
**Q: "Why does my streaming response buffer and arrive all at once in production?"**

**A:**
"This is usually an Nginx or PHP-FPM configuration issue. Nginx buffers output by default. 
To fix this in Laravel, we send the header `X-Accel-Buffering: no` which tells Nginx to disable buffering for this specific response. 
Also, `ob_flush()` and `flush()` must be called in the PHP loop to force the buffer out to the client immediately."
