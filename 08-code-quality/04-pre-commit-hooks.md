# 🧪 04 - Pre-commit Hooks (Husky)

### 🧐 Explanation
"I forgot to run lint!"
Don't let git commit bad code.
**Pre-commit Hook**: A script that runs when you type `git commit`.
If the script fails (lint error, test failure), the commit is **blocked**.

**Tool**: **Husky**.

---

### 💻 Setup

1.  **Install Husky:**
    `npm install husky --save-dev`
    `npx husky install`

2.  **Add Hook:**
    `npx husky add .husky/pre-commit "npm run lint-staged"`

3.  **package.json Config:**

```json
{
  "scripts": {
    "lint-staged": "lint-staged"
  },
  "lint-staged": {
    "**/*.php": [
      "./vendor/bin/pint",
      "git add"
    ],
    "**/*.js": [
      "eslint --fix",
      "git add"
    ]
  }
}
```

---

### ✅ When to Use
1.  **Code Style**: ALWAYS format files before committing.
2.  **JS Linting**: Clean up Javascript/Vue/React files.

### ❌ When NOT to Use
1.  **Running Entire Test Suite**: Don't run `php artisan test` on pre-commit. It takes 2 minutes. Commits should be fast (seconds). Let CI run tests.

---

### 🎙 Interview Insight
**Q: "Why block commits locally instead of failing in CI?"**
**A:** "Feedback loop speed. Local hook = 1 second feedback. CI failure = 5 minutes + context switch + fix commit + push again.
Blocking unformatted code locally keeps the git history clean (no 'fix styling' commits)."
