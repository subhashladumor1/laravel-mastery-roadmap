# 🤖 Laravel MCP: Build Your Own Server

> **"Expose YOUR app to the AI World."**
> The foundational library (`laravel/mcp`) for creating **Model Context Protocol (MCP)** servers in Laravel. While **Boost** is Laravel's pre-built MCP server, `laravel/mcp` is for building custom servers.

---

## 🧐 What is it?
**Laravel MCP** (`laravel/mcp`) is the low-level library that powers Boost.
It allows you to **expose your application's resources (data) and tools (actions) to AI clients** like Claude Desktop, Cursor, and Zed.

**Think of it as**: API Endpoints for LLMs.

**Key Features:**
*   **Resources**: Read-only data access (e.g., `users://active`).
*   **Tools**: Executable functions (e.g., `create_invoice()`).
*   **Prompts**: Reusable prompt templates (e.g., `summarize_logs`).

---

## 🛠 Usage
This is advanced. Use this if you want to build a specialized tool for your team (e.g., A "Ops Bot" that manages production).

### 1. Define a Resource (Safe Read)
Let AI invoke `users://list` to see active users.

```php
use Laravel\Mcp\Resource;
use Laravel\Mcp\Request;

class UserResource extends Resource
{
    public string $uri = 'users://list';
    public string $name = 'User List';

    public function read(Request $request): string
    {
        return User::select('id', 'email', 'status')
            ->active()
            ->get()
            ->toJson();
    }
}
```

### 2. Define a Tool (Action)
Let AI invoke `ticket_create(title, priority)` autonomously.

```php
use Laravel\Mcp\Server\Tool;
use Laravel\Mcp\Request;
use Laravel\Mcp\Response;
use Illuminate\Contracts\JsonSchema\JsonSchema;

class CreateTicketTool extends Tool
{
    public string $name = 'create_ticket';
    public string $description = 'Open a support ticket.';
    
    public function schema(JsonSchema $schema): array
    {
        return [
            'title' => $schema->string()->required(),
            'priority' => $schema->string()->enum(['high', 'low'])->required(),
        ];
    }

    public function handle(Request $request): Response
    {
        $ticket = Ticket::create($request->all());
        
        return Response::text("Ticket #{$ticket->id} created.");
    }
}
```

### 3. Define a Prompt
Reusable context for AI.

```php
use Laravel\Mcp\Server\Prompt;

class SummarizeLogsPrompt extends Prompt
{
    public string $name = 'summarize_logs';
    
    public function messages(Request $request): array
    {
        // ... Fetch logs
        return [
            Message::user("Summary of logs:\n" . $logs)
        ];
    }
}
```

### 4. Register and Serve

**config/mcp.php**:
```php
return [
    'server' => [
        'name' => 'My Laravel Server',
        'version' => '1.0.0',
    ],
    'resources' => [UserResource::class],
    'tools' => [CreateTicketTool::class],
    'prompts' => [SummarizeLogsPrompt::class],
];
```

To expose via HTTP (Server-Sent Events):
`Route::mcp('/mcp/sse');`

---

## ⚡ Best Practices

1.  **Validation**: Use strict validation in `schema()`. AI hallucinates parameters often.
2.  **Audit Logs**: Log every MCP tool execution via `Log::info("Tool used: {$tool->name}", $args)`.
3.  **Authentication**: Secure your endpoints using standard Laravel auth (`Sanctum` or `Basic Auth`) if exposing over HTTP.

---

## 🌍 Real World Example: Database Schema Explorer

You can build a tool that reads your database schema and lets AI execute **safe, read-only SQL queries**.

```php
class RunSqlQuery extends Tool
{
    public string $name = 'run_sql';
    
    public function handle(Request $request): Response
    {
        $sql = $request->input('query');
        
        if (!str_starts_with(strtoupper(trim($sql)), 'SELECT')) {
            return Response::error("Only SELECT queries allowed.");
        }
        
        $results = DB::select($sql);
        
        return Response::json($results);
    }
}
```
