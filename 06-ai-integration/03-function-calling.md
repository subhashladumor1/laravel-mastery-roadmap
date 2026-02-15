# 🤖 03 - Function Calling: Using AI as an Agent

### 🧐 Explanation
Function Calling transforms OpenAI from a "Chatbot" into an "Agent".
Instead of just replying with text, the AI can say:
*"I need to run the `get_weather` function for 'London'."*

Your Laravel app runs the code, gets the result, and feeds it back to the AI.
This allows AI to:
1.  Query your Database (`get_user_orders`).
2.  Call External APIs (`check_stock_levels`).
3.  Perform Actions (`cancel_subscription`).

---

### 💻 Code Example

**File:** `app/Services/AI/AgentService.php`

```php
use OpenAI\Laravel\Facades\OpenAI;

public function chatWithTools(string $userMessage)
{
    $tools = [
        [
            'type' => 'function',
            'function' => [
                'name' => 'get_order_status',
                'description' => 'Get the status of an order by ID',
                'parameters' => [
                    'type' => 'object',
                    'properties' => [
                        'order_id' => [
                            'type' => 'string',
                            'description' => 'The order ID, e.g. ORD-12345',
                        ],
                    ],
                    'required' => ['order_id'],
                ],
            ],
        ],
    ];

    $response = OpenAI::chat()->create([
        'model' => 'gpt-4-turbo',
        'messages' => [['role' => 'user', 'content' => $userMessage]],
        'tools' => $tools,
    ]);

    $message = $response->choices[0]->message;

    // Check if AI wants to call a function
    if ($message->toolCalls) {
        foreach ($message->toolCalls as $toolCall) {
            if ($toolCall->function->name === 'get_order_status') {
                $args = json_decode($toolCall->function->arguments, true);
                
                // execute the actual PHP code
                $status = \App\Models\Order::find($args['order_id'])->status ?? 'Not Found';
                
                // Return result to AI (Step 2)
                return $this->submitToolOutput($toolCall->id, $status); 
            }
        }
    }

    return $message->content;
}
```

---

### ✅ When to Use
1.  **Customer Support Bots**: "Where is my order?" -> AI calls `get_order`.
2.  **Data Analysis**: "How many users signed up last week?" -> AI calls `get_analytics`.
3.  **Action Takers**: "Upgrade me to Pro plan." -> AI calls `update_subscription`.

### ❌ When NOT to Use
1.  **Simple FAQs**: If the answer is static text, don't use function calling.
2.  **Destructive Actions without Confirmation**: Never let AI `delete_user` without a manual confirmation step.

---

### 🎙 Interview Insight
**Q: "How do you secure AI Function Calling?"**
**A:** "Strict validation. Just because the AI *suggests* parameters doesn't mean they are safe. I treat AI input just like user input—it must be validated. I also scope the functions permissions (e.g., read-only access for support bots) and never expose `eval()` or raw SQL execution capabilities to the model."
