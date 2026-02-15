# 📦 03 - Livewire Charts: Dynamic Visualizations (ApexCharts)

### 🧐 Explanation
"I need a sales graph."
**Old**: Learn `Chart.js`, `D3.js`, API endpoints, Vue component, AJAX polling.
**New (Livewire Charts)**:
1.  Install package (`leandrocfe/filament-apex-charts`).
2.  Write PHP array.
3.  Render Blade component.
4.  Get real-time updates (Polling).

---

### 💻 Setup

```bash
composer require leandrocfe/filament-apex-charts
php artisan make:filament-apex-charts BlogPostsChart --type=line
```

---

### 💻 Code Example

**File**: `app/Filament/Widgets/BlogPostsChart.php`

```php
use Leandrocfe\FilamentApexCharts\Widgets\ApexChartWidget;

class BlogPostsChart extends ApexChartWidget
{
    protected int | string | array $columnSpan = 'full';

    protected function getOptions(): array
    {
        $data = Trend::model(Post::class)
            ->between(start: now()->subMonth(), end: now())
            ->perDay()
            ->count();

        return [
            'chart' => [
                'type' => 'line',
                'height' => 300,
            ],
            'series' => [
                [
                    'name' => 'Posts',
                    'data' => $data->map(fn (TrendValue $value) => $value->aggregate),
                ],
            ],
            'xaxis' => [
                'categories' => $data->map(fn (TrendValue $value) => $value->date),
                'labels' => [
                    'style' => [
                        'fontFamily' => 'inherit',
                    ],
                ],
            ],
            'stroke' => [
                'curve' => 'smooth',
            ],
        ];
    }
}
```

---

### ✅ When to Use
1.  **Dashboards**: Sales, Users, Traffic.
2.  **Filament**: It integrates natively.

### ❌ When NOT to Use
1.  **Complex Custom Charts**: Thousands of data points per series (Canvas rendering required). SVG charts (Apex) get slow with 10k points.
2.  **3D/WebGL**: Data visualizations require D3.js or Three.js.

---

### 🎙 Interview Insight
**Q: "How do you optimize chart queries?"**
**A:** "Instead of querying huge datasets on every render (`Post::all()`), I use `flowframe/laravel-trend` package to aggregate data in SQL (`GROUP BY date`).
Also, charts should implement `LazyLoading` or `Polling` only when visible (IntersectionObserver) to avoid killing the DB on dashboard load."
