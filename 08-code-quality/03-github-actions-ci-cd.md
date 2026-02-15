# 🧪 03 - GitHub Actions (CI/CD)

### 🧐 Explanation
You want to deploy multiple times a day.
If you manually SCP files to a VPS, you are **doing it wrong**.
**CI (Continuous Integration)**: Run tests and linters on every PR.
**CD (Continuous Deployment)**: If tests pass, SSH into server and `git pull`.

---

### 💻 CI Workflow: `.github/workflows/laravel.yml`

```yaml
name: Laravel CI

on:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: password
          MYSQL_DATABASE: laravel_test
        ports:
          - 3306:3306

    steps:
    - uses: actions/checkout@v3

    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.3'
        extensions: dom, curl, libxml, mbstring, zip, pcntl, pdo, sqlite, pdo_sqlite, bcmath, soap, intl, gd, exif, iconv
        coverage: none

    - name: Install Dependencies
      run: composer install -q --no-ansi --no-interaction --no-scripts --no-progress --prefer-dist

    - name: Copy .env
      run: php -r "file_exists('.env') || copy('.env.example', '.env');"

    - name: Generate Key
      run: php artisan key:generate

    - name: Directory Permissions
      run: chmod -R 777 storage bootstrap/cache

    - name: Execute Tests (Pest)
      env:
        DB_CONNECTION: mysql
        DB_Database: laravel_test
        DB_PASSWORD: password
      run: vendor/bin/pest
```

---

### ✅ When to Use
1.  **Every Project**: Even solo projects.
2.  **Linting**: Add `vendor/bin/pint --test` step.
3.  **Static Analysis**: Add `vendor/bin/phpstan` step.

### ❌ When NOT to Use
1.  **Never**: CI prevents broken builds.

---

### 🎙 Interview Insight
**Q: "How do you handle secrets (API keys) in GitHub Actions?"**
**A:** "Don't commit `.env`. Instead, use **GitHub Secrets** (Settings -> Secrets -> Actions).
Inject them as Environment Variables in the workflow YAML:
`env: STRIPE_KEY: ${{ secrets.STRIPE_KEY }}`.
Never print secrets to the console log."
