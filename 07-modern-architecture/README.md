# 🏗️ Modern Architecture (Beyond MVC)

### 🎯 Evolution of Laravel
**Standard:** MVC (Model-View-Controller) -> **Modern:** 
1.  **DDD (Domain Driven Design)**
2.  **Hexagonal Architecture (Ports & Adapters)**
3.  **Modular Monolith**
4.  **Microservices**

---

## 📚 Topics

| Topic | Description | Difficulty |
| :--- | :--- | :--- |
| **[01-repository-pattern.md](./01-repository-pattern.md)** | **Stop Over-abstracting!** When is a Repo useful vs useless? | 🟠 Medium |
| **[02-service-layer.md](./02-service-layer.md)** | Where business logic lives. Not in Controllers! | 🟢 Basic |
| **[03-events-listeners.md](./03-events-listeners.md)** | Decoupling side-effects (Email, Slack, Analytics). | 🟠 Medium |
| **[04-pipelines.md](./04-pipelines.md)** | Chain of Responsibility pattern in Laravel. | 🔴 Advanced |
| **[05-cqrs.md](./05-cqrs.md)** | Separating Read (Query) from Write (Command) models. | 🔴 Advanced |

---

## 💡 Philosophy
"Don't implement a pattern just because it's popular. Implement it because it solves a **specific pain point** in your project."
