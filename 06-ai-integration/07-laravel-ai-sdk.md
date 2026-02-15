# 🧠 Laravel AI SDK: The Agentic Intelligence Layer

> **"Agents are the new Controllers."**
> The official **Laravel AI SDK** (`laravel/ai`) is an agent-first library. Instead of loose "chat" calls, you build structured **Agents** that encapsulate persona, tools, and history.

---

## 🧐 What is it?
The **Laravel AI SDK** abstracts LLM implementations (OpenAI, Anthropic, Gemini) into a unified interface.
Its core building block is the **Agent**: a dedicated class that helps you organize AI logic, maintain capabilities, and persist conversation state automatically.

**Key Features:**
*   **Agents**: Dedicated classes (`php artisan make:agent SalesCoach`).
*   **Tool Use**: Bind PHP classes as tools the AI can use.
*   **Structured Output**: Enforce strict JSON schemas.
*   **Multi-Modal**: Audio (TTS/STT), Images, and Embeddings.
*   **Provider Agnostic**: Switch between Claude 3.5 and GPT-4o in config.

---

## 🛠 Installation

```bash
composer require laravel/ai
php artisan vendor:publish --provider="Laravel\Ai\AiServiceProvider"
php artisan migrate
```

This creates `agent_conversations` tables to store chat history automatically.

---

## 💡 The "Agent" Workflow

### 1. Create an Agent
Don't write spaghetti code in controllers. Create an Agent class.

```bash
php artisan make:agent SalesCoach --structured
```

### 2. Define the Agent Logic
Open `App/Ai/Agents/SalesCoach.php`:

```php
namespace App\Ai\Agents;

use App\Models\User;
use Laravel\Ai\Contracts\Agent;
use Laravel\Ai\Contracts\Conversational;
use Laravel\Ai\Promptable;

class SalesCoach implements Agent, Conversational
{
    use Promptable;

    public function __construct(public User $user) {}

    // 1. Instructions (System Prompt)
    public function instructions(): string
    {
        return "You are an expert Sales Coach. Analyze the user's pitch and provide feedback.";
    }

    // 2. Tools (Capabilities)
    public function tools(): array
    {
        return [
            new \App\Ai\Tools\LookUpProductPrice,
            new \App\Ai\Tools\CheckInventory,
        ];
    }
}
```

### 3. Using the Agent
Interact with the agent in your Controller. It feels like Eloquent.

```php
use App\Ai\Agents\SalesCoach;

// Start a conversation
$response = (new SalesCoach($user))
    ->prompt("How do I sell this pen?");

echo $response->text;

// Continue the conversation (Automatically loads history!)
$followUp = (new SalesCoach($user))
    ->continue($response->conversationId)
    ->prompt("Give me 3 more tips.");
```

---

## 🏎 Advanced Features

### Structured Output (JSON)
Enforce the AI to return data, not text. Use `HasStructuredOutput`.

```php
class ExtractorAgent implements Agent, HasStructuredOutput
{
    use Promptable;

    public function schema(JsonSchema $schema): array
    {
        return [
            'name' => $schema->string()->required(),
            'email' => $schema->string()->email()->required(),
            'confidence_score' => $schema->integer()->min(0)->max(100),
        ];
    }
}

// Usage
$data = (new ExtractorAgent)->prompt("John Doe (john@example.com)");
return $data['email']; // "john@example.com"
```

### Streaming to Browser
Send tokens as they arrive (SSE).

```php
Route::get('/optimize', function () {
    return (new CodeReviewAgent)
        ->stream('Review this code...')
        ->then(function ($response) {
             // Log the full result after stream ends
             Log::info($response->text);
        });
});
```

### Audio & Transcriptions
Fluent API for Whisper and TTS.

```php
use Laravel\Ai\Transcription;

// Floating Syntax
$text = Transcription::fromUpload($request->file('audio'))
    ->diarize() // Identify speakers
    ->generate();

// Generate Embeddings
$vector = Str::of('Laravel is cool')->toEmbeddings();
```

---

## ⚡ Best Practices

1.  **One Agent, One Purpose**: Don't make a "God Agent". Make a `SalesAgent`, `SupportAgent`, `CodeFixerAgent`.
2.  **Use `continue()`**: Leverage the built-in state management. You don't need to manually query `Message::where(...)`—the SDK does it.
3.  **Dependency Injection**: Pass Models (`User`, `Order`) into the Agent's `__construct` to use them in `instructions()`.
    ```php
    public function instructions() {
        return "You are helping {$this->user->name}.";
    }
    ```
4.  **Testing**: Use `Ai::fake()` to mock responses during PHPUnit tests.

---

## 🌍 Real World Example: Customer Support Bot

```php
// Route
Route::post('/chat', function (Request $request) {
    $agent = new SupportAgent($request->user());

    if ($request->conversation_id) {
        $agent->continue($request->conversation_id);
    }

    return $agent->stream($request->message)
        ->usingVercelDataProtocol(); // Compatible with Vercel AI SDK Frontend
});
```
