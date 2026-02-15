# 🤖 06 - AI Integration (The Future is Here)

Welcome to the **AI Integration Masterclass**. This module covers how to leverage Large Language Models (LLMs), Embeddings, and Vectors directly within your Laravel applications.

> **Why learn this?**
> Every SaaS is integrating AI. Knowing how to efficiently manage API costs, handle rate limits, and build RAG (Retrieval-Augmented Generation) systems is now a **core skill**.

---

## 📚 Topics Covered

| Topic | Description | Difficulty |
| :--- | :--- | :--- |
| **[01-openai-chat-integration.md](./01-openai-chat-integration.md)** | Basics: Connecting to OpenAI, API Keys, simple chat. | 🟢 Basic |
| **[02-streaming-responses.md](./02-streaming-responses.md)** | **UX Gold**: Stream SSE responses directly to Blade/Livewire. | 🟠 Intermediate |
| **[03-function-calling.md](./03-function-calling.md)** | Let AI modify your database safely. | 🔴 Advanced |
| **[04-vector-embeddings.md](./04-vector-embeddings.md)** | Semantic Search: Storing vectors in PostgreSQL/Pinecone. | 🔴 Advanced |
| **[05-rag-implementation.md](./05-rag-implementation.md)** | **Classic RAG**: Using generic tools (Pinecone/Weaviate). | 🔴 Advanced |
| **[06-cost-management.md](./06-cost-management.md)** | Token Audit: Preventing bankruptcy. | 🟠 Intermediate |
| **[07-laravel-ai-sdk.md](./07-laravel-ai-sdk.md)** | **🔥 OFFICIAL SDK**: The new Agent workflow (`make:agent`). | 🟢 Essential |
| **[08-laravel-boost.md](./08-laravel-boost.md)** | **MCP Server (Dev)**: Give your IDE context about your app. | 🟠 Intermediate |
| **[09-laravel-mcp.md](./09-laravel-mcp.md)** | **MCP Foundation**: Build custom AI tools (`laravel/mcp`). | 🔴 Advanced |
| **[10-ai-real-world-examples.md](./10-ai-real-world-examples.md)** | **Recipes**: 15 Copy-Paste snippets using the new SDK. | 🟢 Basic |

---

## 🛠 Recommended Strategy (2026+)

1.  **Use `laravel/ai`** for all user-facing features (Chat, Agents, RAG).
2.  **Use `laravel/boost`** for your development workflow (IDE Context).
3.  **Use `laravel/mcp`** only if building internal AI tools.

*Legacy Packages (Still good, but secondary)*:
*   `openai-php/laravel`: If you need raw API access.
*   `spatie/laravel-openai`: Excellent simple wrapper.

---

## 💡 Practical Challenge

**Goal**: Build a "Summarize this Article" button for a Blog.
1.  User clicks "Summarize".
2.  Job is dispatched to Queue (don't block the request!).
3.  Worker calls OpenAI.
4.  Result is saved to `posts.summary` column.
5.  Frontend is updated via Echo/Pusher.

👉 [See Solution in 05-practical-tasks/advanced/08-ai-summarizer.md](../05-practical-tasks/advanced/08-ai-summarizer.md)
