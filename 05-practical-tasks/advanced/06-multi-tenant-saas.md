# 🩸 Advanced Task: Multi-Tenant SaaS (Single DB)

### 📌 Problem Description
**"Build a B2B SaaS (Team A cannot see Team B)."**
Single DB setup.

**Requirements**:
1.  **Trait**: `BelongsToTeam`.
2.  **Scope**: `addGlobalScope('team_id')`.
3.  **Middleware**: `EnsureUserIsOnTeam`.
4.  **Invite**: Send email to join team.
5.  **Bonus**: Subdomains (`team-a.saas.com`).

---

### 💻 Solution (Scope)

```php
protected static function bootBelongsToTeam()
{
    static::addGlobalScope('team', function (Builder $query) {
        if (auth()->check()) {
            $query->where('team_id', auth()->user()->current_team_id);
        }
    });

    static::creating(function ($model) {
        $model->team_id = auth()->user()->current_team_id;
    });
}
```

---

### 🧪 Security Test
1.  **Login as User A (Team 1)**.
2.  **Try to view Post ID 99 (Team 2)**.
3.  **Result**: 404 Not Found (Scope hides it).
