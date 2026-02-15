# 🍼 Beginner Task: Todo List API

### 📌 Problem Description
**"Build a REST API for a Todo List."**
Focus on proper HTTP verbs and JSON responses.

**Requirements**:
1.  **GET /todos**: List all tasks (JSON).
2.  **POST /todos**: Create new task (`title`, `completed`=false).
3.  **PUT /todos/{id}**: Update task (`completed`=true).
4.  **DELETE /todos/{id}**: Delete task.
5.  **Validation**: Title is required.

---

### 💻 Solution (Controller)

```php
public function index()
{
    return response()->json(Todo::all());
}

public function store(Request $request)
{
    $request->validate(['title' => 'required']);
    
    $todo = Todo::create([
        'title' => $request->title,
        'completed' => false
    ]);
    
    return response()->json($todo, 201);
}

public function update(Request $request, Todo $todo)
{
    $todo->update($request->only('completed', 'title'));
    return response()->json($todo);
}

public function destroy(Todo $todo)
{
    $todo->delete();
    return response()->json(null, 204);
}
```
