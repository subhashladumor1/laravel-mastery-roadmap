# 🍼 Intermediate Task: Event Booking (Pivot)

### 📌 Problem Description
**"Users book Tickets for an Event."**
Many Users <-> Many Events.

**Requirements**:
1.  **Pivot Table**: `event_user`.
2.  **Fields**: `event_id`, `user_id`, `ticket_type` ('vip', 'regular'), `price`.
3.  **Validate**: Max capacity check.
4.  **UI**: "Book Now" Button.
5.  **Bonus**: Send QR Code via Email.

---

### 💻 Solution (Controller)

```php
// Route::post('/events/{event}/book', [BookingController::class, 'store']);

public function store(Request $request, Event $event)
{
    if ($event->users()->count() >= $event->capacity) {
        return back()->with('error', 'Sold Out!');
    }
    
    // Attach with Pivot Data
    $event->users()->attach(auth()->id(), [
        'ticket_type' => $request->type,
        'price' => $event->getPrice($request->type),
        'created_at' => now(),
    ]);
    
    // Fire Event for Email
    event(new TicketBooked($event, auth()->user()));
}
```
