# 🧠 Advanced Interview Questions: Architect Level

> **"Design a system that handles 1M requests per second."**
> These questions separate the Seniors from the Juniors.

---

## 📚 Topics

| Question | Core Concept | Difficulty |
| :--- | :--- | :--- |
| **[01-container-binding-internals.md](./01-container-binding-internals.md)** | How `app()` works internally (Reflection). | 🔴 Hard |
| **[02-race-conditions.md](./02-race-conditions.md)** | `lockForUpdate()` and Atomic Operations. | 🔴 Hard |
| **[03-memory-leaks-queues.md](./03-memory-leaks-queues.md)** | Managing long-running PHP commands. | 🔴 Hard |
| **[04-scalable-saas-design.md](./04-scalable-saas-design.md)** | Multi-tenancy strategies (Single DB vs Multi DB). | 🔴 Hard |
| **[05-events-vs-queues.md](./05-events-vs-queues.md)** | Decoupling logic for scale. | 🔴 Hard |
| **[06-rapid-fire-questions.md](./06-rapid-fire-questions.md)** | **50+ Senior Questions** (Octane, Horizon, DDD, Patterns). | 🔴 Revision |

---

## 💡 How to Answer
**Don't say:**
> "I just use Redis."

**Say:**
> "Redis is powerful, but for financial transactions, we need durable queues like SQS. If the Redis instance crashes before persistence, we lose jobs. I configure Supervisor to restart workers and catch exceptions to `failed_jobs` table for manual retry."
