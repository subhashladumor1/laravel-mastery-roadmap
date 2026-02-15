# 🩸 Advanced Task: Real-Time Chat (WebSockets)

### 📌 Problem Description
**"Build a Slack Clone."**
Live updates. 0ms delay.

**Requirements**:
1.  **Backend**: `Laravel Reverb` (or Pusher).
2.  **Frontend**: `Laravel Echo` + `Vue/React`.
3.  **Events**: `MessageSent` implements `ShouldBroadcast`.
4.  **Channel**: `private-room.{id}`.
5.  **Bonus**: "User is typing..." presence channel.

---

### 💻 Solution (Controller)

```php
// MessageController.php
public function store(Request $request, Room $room)
{
    $message = $room->messages()->create([
        'body' => $request->body,
        'user_id' => auth()->id()
    ]);

    broadcast(new MessageSent($message))->toOthers();

    return $message;
}
```

---

### 💻 Solution (Frontend - Echo)

```javascript
Echo.private(`room.${roomId}`)
    .listen('MessageSent', (e) => {
        messages.value.push(e.message);
    })
    .whisper('typing', {
        name: user.name
    });
```
