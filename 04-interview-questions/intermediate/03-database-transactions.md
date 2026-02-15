# 🍼 Intermediate: Database Transactions (ACID)

## ❓ The Question
**"Why and when should I use `DB::transaction()`? What does ACID mean?"**

---

### 💡 The Answer (Intermediate)
**Scenario**: User transfers $100 to Friend.
1.  Subtract $100 from User.
2.  Add $100 to Friend.
3.  **Crash!** (Step 2 fails).
**Result**: User lost $100, Friend got nothing. Money disappeared.

**Transaction**:
"All or Nothing."
If Step 2 fails, Step 1 is **rolled back** (undone).

**ACID**:
*   **Atomicity**: Is it one unit? Yes.
*   **Consistency**: Is database valid? Yes.
*   **Isolation**: Can other users interrupt? No.
*   **Durability**: Is it saved permanently? Yes.

---

### 💻 Code Example

**❌ Bad (No Transaction):**
```php
$user->balance -= 100;
$user->save();

// If this crashes, $user lost money!
$friend->balance += 100;
$friend->save();
```

**✅ Good (Transaction):**
```php
DB::transaction(function () use ($user, $friend) {
    $user->balance -= 100;
    $user->save();

    $friend->balance += 100;
    $friend->save();
});
// If anything throws Exception, DB automatically rolls back.
```

---

### 🔄 Follow-Up Question
**Q: "Can I use `try-catch` inside transaction?"**
**A:** "Yes, but be careful. If you catch the exception and don't re-throw it, the transaction might commit accidentally. Better to let Laravel handle it or manually call `DB::rollBack()` in the catch block."
