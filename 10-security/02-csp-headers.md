# 🔐 02 - Content Security Policy (CSP Headers)

### 🧐 Explanation
CSP is the **Last Line of Defense** against XSS.
It tells the browser: "I only load JS from `google.com` and `my-site.com`. Everything else (like `evil-script.com`) is **blocked**."

Even if hackers inject `<script src="http://hackerable.com/hack.js">`, CSP blocks the connection.

---

### 💻 Implementation

**Install Package**: `spatie/laravel-csp`
This is annoying to configure manually (long headers). Use Spatie's package.

**Policy Class**: `app/Services/Csp/Basic.php`

```php
use Spatie\Csp\Directive;
use Spatie\Csp\Policies\Policy;

class Basic extends Policy
{
    public function configure()
    {
        $this
            ->addDirective(Directive::BASE, 'self')
            ->addDirective(Directive::CONNECT, 'self')
            ->addDirective(Directive::DEFAULT, 'self')
            ->addDirective(Directive::FORM_ACTION, 'self')
            ->addDirective(Directive::IMG, [
                'self',
                'data:', // Allow base64 images
                '*.gravatar.com', // External Gravatar
            ])
            ->addDirective(Directive::MEDIA, 'self')
            ->addDirective(Directive::OBJECT, 'none')
            ->addDirective(Directive::SCRIPT, [
                'self',
                'unsafe-eval', // Required for Vue/Livewire devtools sometimes
                'https://apis.google.com', // Allow Google Maps
            ])
            ->addDirective(Directive::STYLE, [
                'self',
                'unsafe-inline', // Allow inline <style>...</style> (Often required)
            ]);
    }
}
```

**Provider**: `config/csp.php`

```php
'policy' => App\Services\Csp\Basic::class,
'enabled' => env('CSP_ENABLED', false),
```

---

### ✅ When to Use
1.  **High Security**: Any SaaS storing PII/Payment data.
2.  **Audit Compliance**: PCI-DSS requires stringent headers.

### ❌ When NOT to Use
1.  **Development**: It breaks hot reloading (Vite/Webpack uses websockets for HMR). Disable in `.env.local`.
2.  **User-Generated Content**: If users can embed YouTube videos, you must whitelist `youtube.com` in `FRAME` directive.

---

### 🎙 Interview Insight
**Q: "What is `nonce` (Number used ONCE) in CSP?"**
**A:** "Instead of `unsafe-inline`, we generate a unique cryptographic token for every Request (`nonce-R4nd0mStr1ng`).
We include this in our script tags: `<script nonce="{{ csp_nonce() }}">`.
The browser executes only scripts with the matching nonce. Even if XSS injects a script, it won't have the nonce, so it fails."
