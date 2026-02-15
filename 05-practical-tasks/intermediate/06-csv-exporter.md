# 🍼 Intermediate Task: CSV Exporter (Queued)

### 📌 Problem Description
**"Admin clicks 'Export Users'. It takes 5+ minutes."**
Don't time out the browser.

**Requirements**:
1.  **Job**: `ExportUsersJob`.
2.  **Logic**: `User::chunk` -> Write to CSV.
3.  **Notify**: Send email with download link when done.
4.  **UI**: "We are generating your report. Check email soon."
5.  **Bonus**: Real-time progress bar.

---

### 💻 Solution (Job)

```php
public function handle()
{
    $filename = 'users-export-'.now()->timestamp.'.csv';
    $path = storage_path('app/public/'.$filename);
    
    $file = fopen($path, 'w');
    fputcsv($file, ['ID', 'Name', 'Email']);
    
    User::chunk(1000, function ($users) use ($file) {
        foreach ($users as $user) {
            fputcsv($file, [$user->id, $user->name, $user->email]);
        }
    });
    
    fclose($file);
    
    // Notify
    Mail::to($this->admin)->send(new ReportReady($filename));
}
```
