# 🤖 Advanced Task: AI Content Generator (+ WebSockets)

### 📌 Problem Description
**"Build a Blog Auto-Generator where users type a title ('Top 10 Larvel Tips') and the AI writes, formats, and publishes the post in real-time."**

**Core Requirements:**
1.  **Queue**: Generation takes 30s. Don't block HTTP.
2.  **Streaming**: Show progress ("Drafting... 20%", "Writing Section 1...").
3.  **Broadcasting**: Use Laravel Reverb/Pusher to push updates to frontend.
4.  **Database**: Save the final Markdown.

---

### 📐 Architecture Diagram (ASCII)

```
[ User ] --(1. Submit Title)--> [ API: /generate ] 
                                      |
                             [ Job: GeneratePostJob ] 
                                      |
         (2. Call OpenAI) <-----------+
                 |
         (3. Stream Chunks)
                 |
      [ Laravel Event: PostUpdating ] --(4. Broadcast)--> [ WebSocket Server ]
                                                                 |
                                                          [ Frontend (Live Update) ]
```

---

### 💻 Implementation Guide

**1. Create a Channel:** `routes/channels.php`
```php
Broadcast::channel('posts.{id}', function ($user, $id) {
    return (int) $user->id === (int) Post::find($id)->user_id;
});
```

**2. The Job (With Streaming):**
**File:** `app/Jobs/GeneratePostJob.php`

```php
public function handle()
{
    $post = $this->post;
    $post->update(['status' => 'generating']);

    $stream = OpenAI::chat()->createStreamed([
        'model' => 'gpt-4',
        'messages' => [['role' => 'user', 'content' => "Write a blog about: {$post->title}"]],
    ]);

    $fullContent = '';

    foreach ($stream as $response) {
        $chunk = $response->choices[0]->delta->content ?? '';
        $fullContent .= $chunk;
        
        // Broadcast every ~50 chars to avoid flooding WS
        if (strlen($fullContent) % 50 === 0) {
            broadcast(new PostUpdating($post->id, $fullContent));
        }
    }

    $post->update(['content' => $fullContent, 'status' => 'published']);
    broadcast(new PostPublished($post->id));
}
```

---

### 🧪 Test Cases (Pest PHP)

**Test 1: Job Dispatched**
```php
it('dispatches job and broadcasts event', function () {
    Queue::fake();
    Event::fake();

    $user = User::factory()->create();
    actingAs($user)->postJson('/api/posts', ['title' => 'My first AI post']);

    Queue::assertPushed(GeneratePostJob::class);
});
```

---

### 🚀 Deployment Steps

1.  **Reverb**: `php artisan reverb:start`.
2.  **Supervisor**: `php artisan queue:work`.
3.  **Echo**: Configure frontend to listen to `private-posts.1`.
