# 💎 Advanced Code Quality & Architecture

> **"Code is liability. Less code is better code."**
> A comprehensive guide to writing maintainable, testable, and robust Laravel applications.

---

## 🏗️ 1. SOLID Principles in Laravel

### S - Single Responsibility Principle (SRP)
**Bad**: `UserController` handling validation, database logic, email sending, and response formatting.
**Good**:
*   `StoreUserRequest` (Validation)
*   `CreateUserAction` (Business Logic)
*   `UserResource` (Response Format)
*   `UserRegistered` Event (Email Sending)

### O - Open/Closed Principle
**Bad**: Modification of `PaymentService` class every time a new gateway (Stripe, Paypal) is added.
**Good**: use `PaymentGatewayInterface`. Create `StripeGateway` and `PaypalGateway`. Inject interface.

### L - Liskov Substitution Principle
**Bad**: `Admin` extends `User` but throws exception in `save()` because admins are read-only.
**Good**: Separate interfaces. `ReadOnlyUserInterface`.

### I - Interface Segregation Principle
**Bad**: `VehicleInterface` has `fly()` method. `Car` implements it but throws "Cars can't fly".
**Good**: `FlyableInterface`, `DrivableInterface`.

### D - Dependency Inversion Principle
**Bad**: `OrderController` creates `new Mailer()`.
**Good**: `OrderController` asks for `MailerInterface` in constructor. Laravel binding resolves it.

---

## 🧹 2. Refactoring Patterns

### Early Return (Guard Clauses)
**Bad**:
```php
if ($user) {
    if ($user->isAdmin) {
        // Logic
    }
}
```
**Good**:
```php
if (!$user || !$user->isAdmin) {
    return; // Fast fail
}
// Logic (No nesting)
```

### Action Classes (Command Pattern)
Instead of monolithic Service classes, use Actions for single tasks.
`ExecutePaymentAction`, `UpdateUserProfileAction`.
Why? Easier to test, easier to use in queue jobs, easier to call from CLI.

### Data Transfer Objects (DTOs)
Don't pass associative arrays `['name' => 'John']` deep into your system. You lose type safety.
Use `UserData` object: `public string $name;`.
Package: `spatie/laravel-data`.

---

## 🧪 3. Advanced Testing Strategy

### The Testing Pyramid
1.  **Unit Tests (70%)**: Test small classes (Actions, Helpers) in isolation. Mock everything. Fast.
2.  **Integration Tests (20%)**: Test interaction with DB/Redis. `RefreshDatabase`. Slower.
3.  **E2E Tests (10%)**: Dusk. Test full browser flow. Very slow.

### Mutation Testing (`infection/infection`)
It modifies your code (changes `+` to `-`, removes `if` statements) and runs your tests.
If tests **pass** (mutant survives), your tests are weak.
If tests **fail** (mutant killed), your tests are strong.

### Static Analysis
*   **PHPStan (Level 9)**: No strict type errors.
*   **Rector**: Automatically upgrades code (e.g., PHP 8.1 Enums).
*   **Deptrac**: Enforce architectural rules (e.g., Models cannot depend on Controllers).

---

## 🖌️ 4. Clean Code Checklist
1.  **Naming**: `getUserData($id)` vs `find($id)`. Be specific.
2.  **Magic Numbers**: `if ($status == 1)` vs `if ($status == Status::Active)`.
3.  **Comments**: If you need a comment to explain *what* code does, the code is bad. Refactor it.
4.  **Formatting**: Use `Laravel Pint`. Don't argue about braces. Automation wins.

---

## 🧘 5. Cognitive Load
*   **Reduce Indentation**: Extract methods.
*   **Reduce Abstraction**: Don't create an Interface if you only have one implementation and never plan to test it. YAGNI (You Ain't Gonna Need It).

---

## 👮 6. Security Architecture
*   **Inputs**: Trust nothing. Validate everything in FormRequests.
*   **Outputs**: Escape everything.
*   **Secrets**: Rotate keys regularly. Use specialized vaults (AWS KMS).
*   **Audit**: Log every admin action (`spatie/laravel-activitylog`).
