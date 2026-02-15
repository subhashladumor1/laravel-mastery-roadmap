# 🔄 Continuous Integration & Deployment (CI/CD) Pipeline

> **"From `git push` to Production."**
> A visualization of a modern Laravel deployment workflow using GitHub Actions.

---

## 🟢 1. The Trigger (Git Push)
When a developer [Developer A] pushes code to the repository.

```mermaid
graph TD
    Dev[Developer] -->|Push feature/auth| GitHub
    GitHub -->|Workflow Trigger| Actions[Start GitHub Actions Runner]
    Actions --> Job1[Install Composer Dependencies]
```

## 🧪 2. The Build Pipeline (CI)
Automated checks to ensure quality.

```mermaid
graph LR
    Job1 --> Lint[Run Pint / PHP-CS-Fixer]
    Lint -->|Pass| Static[Run PHPStan Static Analysis]
    Static -->|Pass| Test[Run Pest/PHPUnit Tests]
    Test -->|Fail| Notification[Notify Slack/Email Failure]
    Test -->|Pass| Build[Build Frontend NPM Run Build]
    Build -->|Artifact| Deploy[Ready to Deploy]
```

## 🚀 3. The Deployment (CD)
Zero-downtime deployment to production (Forge/Envoyer/Vapor).

```mermaid
graph TD
    Deploy -->|SSH| Server[Production Server]
    Server -->|Clone| Release[New Release Directory /releases/2026...]
    Release -->|Composer| Install[Composer Install --no-dev]
    Install -->|Artisan| Migrate[php artisan migrate --force]
    Migrate -->|Symlink| Link[ln -s /releases/2026 /current]
    Link -->|Restart| FPM[Reload PHP-FPM & Supervisor]
    FPM -->|Finish| Live[Site is Live!]
```

## 🔄 4. Database Migrations (Zero Downtime)
Handling schema changes without locking.

```mermaid
graph LR
    Step1[Old Code Running] -->|Deploy Start| DB[Database]
    DB --> Alter[ADD COLUMN Allow NULL] -.- Note[Safe!]
    Alter -->|Code Switch| Step2[New Code Uses New Column]
    Step2 --> Cleanup[DROP COLUMN Old]
    Cleanup --> Done
```
