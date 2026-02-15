# 🚀 Laravel Boost: The AI Development Assistant

> **"Give your IDE superhuman context."**
> A first-party **MCP Server** that exposes your complete codebase structure, database schema, and project context to external AI assistants (Claude Code, Cursor, Zed).

---

## 🧐 What is it?
**Laravel Boost** (`laravel/boost`) is for **development time**.
It acts as a middleware between your project and your AI coding assistant.
Instead of pasting snippets into ChatGPT, Boost allows your IDE AI to query your app directly via the **Model Context Protocol (MCP)**.
It knows your app better than you do.

**Key Features:**
*   **Documentation API**: 17,000+ pieces of verified Laravel ecosystem knowledge (avoids hallucinations).
*   **Schema Tools**: Inspect database tables, Eloquent models, and Relationships.
*   **Route Awareness**: Knows all defined routes and middleware.
*   **Guidelines & Skills**: Generates project-specific `CLAUDE.md`, `AGENTS.md`.

---

## 🛠 Installation (Dev Only)

```bash
composer require laravel/boost --dev
php artisan boost:install
```

This creates configuration files in your project root:
*   `.mcp.json`: Config for the MCP server.
*   `CLAUDE.md`: Customized instructions for Claude.
*   `AGENTS.md`: Agent personas.
*   `boost.json`: Fine-grained control over what gets exposed.

**Connect to Claude Desktop**:
Add this to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "my-laravel-app": {
      "command": "php",
      "args": ["artisan", "mcp:serve"]
    }
  }
}
```

---

## 💡 How it Works

### 1. Accurate Refactoring
**User (in Claude Desktop)**: "Refactor `UserController` to use Action classes."

**Claude (via Boost)**:
1.  Calls `boost:read_file('app/Http/Controllers/UserController.php')`.
2.  Calls `boost:list_routes()` to see what routes hit this controller.
3.  Calls `boost:schema('users')` to understand the `User` model relationships.
4.  Generates code that perfectly matches your existing Action class pattern.

### 2. Documentation Lookups
**User**: "How do I implement specific Queue constraints in Laravel 12?"

**Claude (via Boost)**:
1.  Calls `boost:docs_search('queue constraints laravel 12')`.
2.  Boost retrieves chunks from the embedded knowledge base.
3.  Claude answers with accurate, version-specific syntax.

### 3. Agent Skills
Boost comes with pre-defined "skills" for agents.
*   `feature:scaffold`: Generates Model, Migration, Factory, Test, Controller, Policy.
*   `test:run`: Executes pests tests and feeds failure output back to the AI.

---

## ⚡ Best Practices

1.  **Keep it Updated**: Run `php artisan boost:update` regularly to sync the documentation embeddings with the latest framework releases.
2.  **Custom Guidelines**: Edit `CLAUDE.md` to add team-specific rules (e.g., "Always use strict types", "Never use Facades in Services").
3.  **Git Ignore**: Ensure generated files (`.mcp.json`, `CLAUDE.md`) are ignored or committed based on team policy. Usually committed for shared context.

---

## 🌍 Real World Example: The "Smart" Fix

**Scenario**: A failing test in CI.

**Without Boost**:
"Heads down" debugging. Reading logs. Guessing.

**With Boost**:
You ask Claude/Cursor: *"Fix the failing test `ExampleTest`."*
1.  Boost runs the test. Captures the failure output.
2.  Boost reads the source code (`ExampleTest.php`).
3.  Boost reads the SUT (System Under Test).
4.  Boost suggests a fix.
5.  Boost runs the test again to verify.
**Result**: Autonomously fixed bug in seconds.
