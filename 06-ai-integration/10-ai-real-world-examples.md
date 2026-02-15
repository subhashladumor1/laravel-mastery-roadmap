# 🤖 15 Real-World Laravel 12 AI Integrations

> **"From Theory to Practice."**
> Practical, copy-paste snippets using the *new* `Agent` pattern.

---

### 1. 📧 Email Sentiment Agent (Structured)
**Objective**: Prioritize angry customer emails.
**Tool**: `make:agent SentimentAnalysis --structured`

```php
class SentimentAgent implements Agent, HasStructuredOutput
{
    use Promptable;

    public function instructions(): string
    {
        return "Analyze sentiment. Output strictly JSON.";
    }

    public function schema(JsonSchema $schema): array
    {
        return [
            'sentiment' => $schema->string()->enum(['POSITIVE', 'NEGATIVE', 'NEUTRAL']),
            'confidence' => $schema->integer()->min(0)->max(100),
        ];
    }
}

// Usage
$result = (new SentimentAgent)->prompt($email->body);
if ($result['sentiment'] === 'NEGATIVE') {
    // Escalate
}
```

### 2. 📝 SEO Blog Post Generator (Streaming)
**Objective**: Generate content based on a keyword.
**Tool**: `stream()`

```php
Route::get('/generate-seo', function () {
    return (new SeoWriterAgent)
        ->stream("Write a blog about 'Laravel 12 Features'")
        ->usingVercelDataProtocol(); // For React/Vue frontend
});
```

### 3. 🔍 Smart Knowledge Base (RAG)
**Objective**: "Find documentation relevant to X."
**Tool**: `Embeddings` + `Vector Search`

```php
// 1. Ingest
$vector = Str::of($doc->content)->toEmbeddings();
$doc->update(['embedding' => $vector]);

// 2. Search
$queryVector = Str::of($userQuery)->toEmbeddings();
$results = Document::nearest($queryVector)->take(3)->get();

// 3. Agent Context
return (new SupportAgent)
    ->withContext($results)
    ->prompt($userQuery);
```

### 4. 🖼️ Image Description (Vision)
**Objective**: Generate Alt Text.
**Tool**: `withImage()`

```php
$description = (new VisionAgent)
    ->withImage(Storage::get('hero.jpg'))
    ->prompt("Describe this image for screen readers.");

$post->update(['alt_text' => $description->text]);
```

### 5. 🗣️ Voice Note Transcription
**Objective**: Convert meeting recordings.
**Tool**: `Transcription`

```php
$transcript = Transcription::fromUpload($request->file('audio'))
    ->diarize() // "Speaker A: Hello"
    ->generate();

MeetingNote::create(['content' => $transcript]);
```

### 6. 📊 Text-to-SQL (Safe Tool)
**Objective**: Allow admin to query data naturally.
**Tool**: `HasTools`

```php
class SqlAgent implements Agent, HasTools
{
    public function tools(): array
    {
        return [new RunSafeSelectQueryTool]; // Only allows SELECT
    }
}

// "Show me users signed up last week"
$response = (new SqlAgent)->prompt("Show me users signed up last week");
```

### 7. 🐞 Automated Code Review (Diff Analysis)
**Objective**: Review PR.
**Tool**: `Agent`

```php
$diff = `git diff HEAD~1`;
$review = (new CodeReviewerAgent)
    ->prompt("Review this diff for security flaws:\n" . $diff);

GitHub::comment($prId, $review->text);
```

### 8. 🎫 Support Ticket Drafter
**Objective**: Draft a reply based on similar resolved tickets.
**Tool**: `RAG` + `Agent`

```php
$similar = Ticket::search($newTicket->subject)->get(); // Scout/Meilisearch
$draft = (new SupportAgent)
    ->withContext($similar)
    ->prompt("Draft a reply based on these similar resolved tickets.");
```

### 9. 🌍 JSON Translator (Structured)
**Objective**: Translate i18n files.
**Tool**: `Structured Output`

```php
class TranslatorAgent implements Agent, HasStructuredOutput
{
    public function schema($s): array {
        return ['translation' => $s->string()];
    }
}

foreach ($enKeys as $key => $val) {
    $es = (new TranslatorAgent)
        ->system("Translate to Spanish")
        ->prompt($val);
    
    $esJson[$key] = $es['translation'];
}
```

### 10. 👤 Profile Generator
**Objective**: Bio from LinkedIn PDF.
**Tool**: `File Attachment`

```php
$bio = (new BioGeneratorAgent)
    ->withFile(storage_path('resume.pdf'))
    ->prompt("Write a 3-line bio from this resume.");
```

### 11. 🏷️ Auto-Tagging
**Objective**: Tag articles.
**Tool**: `Enum Schema`

```php
class TaggerAgent implements Agent, HasStructuredOutput
{
    public function schema($s): array {
        return [
            'tags' => $s->array()->items($s->string()->enum(['Tech', 'Life', 'News']))
        ];
    }
}
```

### 12. 📅 Event Extractor
**Objective**: "Lunch at 2pm tomorrow" -> `2024-10-25 14:00`.
**Tool**: `Structured Output`

```php
$event = (new CalendarAgent)->prompt($emailBody);
// Returns ['title' => 'Lunch', 'start' => '2024-10-25 14:00']
```

### 13. 📄 Contract Summarizer
**Objective**: Legal review.
**Tool**: `Long Context`

```php
$summary = (new LegalAgent)
    ->prompt("Summarize liability clauses in this 50-page PDF:\n" . $pdfText);
```

### 14. 🛒 Product Recommender
**Objective**: "Hiking gear".
**Tool**: `Vector Search`

```php
$embedding = Str::of("hiking boots")->toEmbeddings();
$products = Product::nearest($embedding)->get();
```

### 15. 🛡️ Content Moderation
**Objective**: Filter comments.
**Tool**: `Agent`

```php
$check = (new ModeratorAgent)->prompt($comment->body);
if ($check->text === 'UNSAFE') {
    $comment->delete();
}
```
