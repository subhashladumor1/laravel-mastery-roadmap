# 🗃️ Deep Dives: Service Container & Facades

> **"The Heart of Laravel."**
> Understanding how dependency injection, bindings, and facades work under the specific hood.

---

## 🟢 1. The Container (IoC)
The container is a class that manages dependencies (`new Class()`).

### Binding Strategies

**1. Binding (`bind`)**
Creates a *new instance* every time you resolve it.

```php
$app->bind('HelpSpot\API', function ($app) {
    return new HelpSpot\API($app->make('HttpClient'));
});
// (New API) !== (New API)
```

**2. Singleton (`singleton`)**
Creates the instance *once* and shares it.

```php
$app->singleton('HelpSpot\API', function ($app) {
    return new HelpSpot\API($app->make('HttpClient'));
});
// (Static API) === (Static API)
```

**3. Instances (`instance`)**
Bind an already existing object.

```php
$api = new HelpSpot\API;
$app->instance('HelpSpot\API', $api);
```

---

## 🟠 2. Automatic Injection (Resolving)
You don't need to bind everything. Laravel uses Reflection.

```php
class PodcastController extends Controller {
    // Laravel inspects the type hint: App\Services\Transistor
    // It sees class exists.
    // It calls 'new Transistor()'.
    public function __construct(protected Transistor $transistor) {}
}
```

---

## 🔴 3. Contextual Binding (Interfaces)
When two classes need different implementations of the *same interface*.

```php
$app->when(PhotoController::class)
    ->needs(Filesystem::class)
    ->give(function () {
        return Storage::disk('local');
    });

$app->when(VideoController::class)
    ->needs(Filesystem::class)
    ->give(function () {
        return Storage::disk('s3');
    });
```

---

## 🟣 4. Facades: The Static Proxy
Facades provide a "static" interface to classes available in the service container.

**The Magic Method (`__callStatic`)**

```php
// Illuminate\Support\Facades\Cache
protected static function getFacadeAccessor() {
    return 'cache'; // Key in the container
}
```

When you call `Cache::get('key')`:
1.  Laravel resolves `app('cache')` from the container.
2.  It calls `$instance->get('key')` on that object.
3.  It looks static, but it's dynamic.
