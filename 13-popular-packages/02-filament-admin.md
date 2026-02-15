# 📦 02 - Filament Admin: TALL Stack Magic

### 🧐 Explanation
Need an Admin Panel?
**Old Way**:
1.  Nova (Paid).
2.  Voyager (Old).
3.  Write it yourself (3 months).

**New Way (Filament)**:
1.  Free.
2.  TALL Stack (Tailwind, Alpine, Livewire, Laravel).
3.  Gorgeous dark mode UI.
4.  Resource-based.

**Command**: `php artisan make:filament-resource User`.

---

### 💻 Setup

```bash
composer require filament/filament
php artisan filament:install --panels
```

---

### 💻 Code Example (UserResource)

**File**: `app/Filament/Resources/UserResource.php`

```php
public static function form(Form $form): Form
{
    return $form
        ->schema([
            TextInput::make('name')->required()->maxLength(255),
            TextInput::make('email')->email()->required()->unique(ignoreRecord: true),
            Select::make('roles')
                ->relationship('roles', 'name')
                ->multiple()
                ->preload(),
        ]);
}

public static function table(Table $table): Table
{
    return $table
        ->columns([
            TextColumn::make('name')->searchable()->sortable(),
            TextColumn::make('email')->icon('heroicon-o-envelope'),
            TextColumn::make('created_at')->dateTime(),
        ])
        ->filters([
            // Filter by "Verified"
            Filter::make('verified')->query(fn ($query) => $query->whereNotNull('email_verified_at')),
        ])
        ->actions([
            EditAction::make(),
            DeleteAction::make(),
        ]);
}
```

---

### ✅ When to Use
1.  **Every App**: Unless you have a specific reason NOT to. It's the standard for Admin Panels in 2024.
2.  **Rapid Prototype**: Build CRUD in 5 mins.

### ❌ When NOT to Use
1.  **Public Frontend**: Filament is for Admins. Don't use it for client-facing e-commerce store (unless using Filament Forms/Tables standalone).
2.  **Complex Custom UI**: If your design is wildly different from standard layout, you will fight the framework.

---

### 🎙 Interview Insight
**Q: "Is Filament just Livewire?"**
**A:** "Yes. Every component (Table, Widget, page) is a Livewire component.
This means you can access `$this` state, emit events, run background jobs, and interact directly with PHP classes without writing Vue/React API layers."
