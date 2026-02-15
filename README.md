# 🚀 Laravel Mastery Roadmap (Laravel 12+)

> **"From Junior to Principal Architect."**
> The complete Laravel 12+ Roadmap. From Junior to Architect. Covers **Advanced Architecture**, **AI Agents** (`laravel/ai`), **300+ Interview Questions**, and **System Design**.

![Laravel Mastery](https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg)

---

## 🌟 Why This Roadmap?

Most tutorials stop at "How to make a CRUD app". **This roadmap starts where they end.** 
We focus on **Enterprise Architecture, Scalability, and Clean Code**.

👉 **[View the Complete Visual Roadmap (Junior -> Architect)](./diagrams/00-full-stack-roadmap.md)**

### 🎯 Core Pillars
*   **🏗️ Architecture**: DDD, Modular Monoliths, Microservices, Event Sourcing.
*   **🤖 AI Engineering**: Build Agents (`laravel/ai`), RAG Systems, and MCP Servers (`laravel/boost`).
*   **⚡ Performance**: Octane, Queues, Caching, Database Indexing.
*   **🛡️ Security**: CSP, XSS protection, Sanctum/Passport flows.
*   **🧪 Quality**: TDD (Pest), Static Analysis (Larastan), Standardization (Pint).

---

## 📚 Table of Contents

| Section | Description | Status |
| :--- | :--- | :--- |
| **[01-basics](./01-basics)** | Solid foundations, clean code, and Modern PHP 8.4+ features. | ✅ Ready |
| **[02-intermediate](./02-intermediate)** | Eloquent relationships, Middleware, and API development. | ✅ Ready |
| **[03-advanced](./03-advanced)** | Service Container, Pipelines, Macros, and deep internals. | 🚧 In Progress |
| **[04-interview-questions](./04-interview-questions)** | 300+ QA: Beginner to Senior Architect levels. | ✅ Complete |
| **[05-practical-tasks](./05-practical-tasks)** | Real-world challenges with solutions & "How To Start". | ✅ Complete |
| **[06-ai-integration](./06-ai-integration)** | **(HOT)** Official AI SDK, Agents, RAG, and MCP Tools. | 🔥 Updated |
| **[07-modern-architecture](./07-modern-architecture)** | DDD, CQRS, Hexagonal, and Modular Monolith. | 🚧 In Progress |
| **[08-code-quality](./08-code-quality)** | Pint, PHPStan, Rectors, and Pre-commit hooks. | ✅ Ready |
| **[09-testing](./09-testing)** | Pest vs PHPUnit, TDD, and Browser Testing. | 🚧 In Progress |
| **[10-security](./10-security)** | XSS, CSRF, CSP, and Secure Authentication flows. | 🚧 In Progress |
| **[11-performance](./11-performance)** | Caching, Indexing, Queues, and Horizon. | 🚧 In Progress |
| **[12-devops](./12-devops)** | Docker, CI/CD, K8s, and Server Management. | 🚧 In Progress |
| **[13-popular-packages](./13-popular-packages)** | Spatie, Filament, Livewire, and best practices. | ✅ Ready |
| **[14-laravel-ecosystem](./14-laravel-ecosystem)** | Sail, Sanctum, Scout, Telescope, Nova. | ✅ Ready |
| **[15-learning-resources](./15-learning-resources)** | Books, Courses, and People to follow. | ✅ Ready |
| **[diagrams](./diagrams)** | **(New)** 50+ Visuals + **[Full Stack Roadmap](./diagrams/00-full-stack-roadmap.md)**. | 🎨 Beautiful |

---

## 🔥 Featured: The "Advanced" Difference

Every tip in this repository follows a strict **Professional Format**:

1.  **Concept**: Clear explanation.
2.  **Code**: Production-ready example (not `foo/bar`).
3.  **When to Use**: Architectural context.
4.  **When NOT to Use**: Anti-patterns.
5.  **Interview Insight**: How to answer this in a senior interview.

### Example: Advanced Middleware

Instead of just "how to create middleware", we cover:
*   [Dynamic Middleware Construction](./02-intermediate/middleware/dynamic-construction.md)
*   [Middleware Parameterization](./02-intermediate/middleware/parameterization.md)
*   [Global vs Group vs Route Middleware Performance](./02-intermediate/middleware/performance.md)

---

## 🤖 AI Integration (The Future is Here)

We don't just use ChatGPT. We build **AI-Powered Laravel Apps** using the official **Laravel 12 AI SDK**.

*   **[Laravel AI SDK](./06-ai-integration/07-laravel-ai-sdk.md)**: Unified Agentic Workflow (`make:agent`).
*   **[Laravel Boost](./06-ai-integration/08-laravel-boost.md)**: Your IDE's best friend (MCP Server).
*   **[Laravel MCP](./06-ai-integration/09-laravel-mcp.md)**: Build custom AI tools for your team.
*   **RAG & Vectors**: Chat with your Database using first-party integrations.

👉 **[Go to AI Section](./06-ai-integration)**

---

## 🎨 Visual Library (Diagrams)

Understanding complex concepts is easier with visuals. We have a dedicated library of **Mermaid.js diagrams** covering:

*   **[The Request Lifecycle](./diagrams/01-request-lifecycle.md)**
*   **[Service Container Resolution](./diagrams/02-service-container.md)**
*   **[Facade Pattern Internals](./diagrams/03-facade-pattern.md)**
*   **[Job Queue Architecture](./diagrams/04-job-queue-flow.md)**
*   **[50+ Comprehensive Diagrams](./diagrams/50-comprehensive-diagrams.md)** (Full Collection)

---

## 🛠 Contribution

We welcome PRs! Please follow the `CONTRIBUTING.md` guidelines.
1.  Fork the repo.
2.  Create a branch `feature/new-tip`.
3.  Ensure code is formatted with **Laravel Pint**.
4.  Submit PR.

---

## 📄 License

This repository is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).

---

### ⭐ Star this repo if you found it useful! 
It helps us create more content.
