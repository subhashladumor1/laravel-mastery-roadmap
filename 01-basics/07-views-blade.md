# 🗃️ Views & Blade: The Frontend Layer

> **"Templating shouldn't be painful."**
> Understanding how to structure your views with Components (Layouts/Partials) and basic Directives.

---

## 🟢 1. Components (Modern) vs Layouts (Legacy)
Most Laravel 8/9+ projects prefer **Components**.

### Anonymous Components (`resources/views/components/alert.blade.php`)
```html
<div class="alert alert-{{ $type }}">
    {{ $slot }}
</div>
```

**Usage:**
```html
<x-alert type="error">
    Something went wrong!
</x-alert>
```

### Passing Attributes
```html
// Input Component
<input {{ $attributes->merge(['class' => 'border-gray-300']) }}>
```

**Usage (Attributes fall through):**
```html
<x-input class="text-red-500" required />
```

---

## 🟠 2. Control Structures (Directives)
Don't write PHP in your views.

**Looping:**
```blade
@foreach ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users found.</p>
@endforeach
```

**Conditions:**
```blade
@auth
    <a href="/dashboard">Dashboard</a>
@endauth

@guest
    <a href="/login">Login</a>
@endguest
```

---

## 🔴 3. Include vs Extend (Why Components Won)
Components isolate logic better than `@include`.

**Legacy:**
```blade
@extends('layouts.app')

@section('content')
    <h1>Hello</h1>
@endsection
```

**Modern (Cleaner):**
```html
<x-app-layout>
    <x-slot:header>
        Dashboard
    </x-slot>

    <h1>Hello</h1>
</x-app-layout>
```
