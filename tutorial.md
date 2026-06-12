# Tutorial: Creating Quiz Questions for the Pro Quiz Master Application

This tutorial explains how to create quiz questions in the correct format for the Pro Quiz Master application. The application uses a specific JSON structure and supports special formatting features including Java/SQL code blocks and markdown-like syntax.

## JSON Structure Overview

Each quiz is a JSON array containing question objects. Here's the basic structure:

```json
[
  {
    "id": 1,
    "question": "Your question text here",
    "link": "https://www.examveda.com/...",
    "options": [
      "Option A",
      "Option B",
      "Option C",
      "Option D"
    ],
    "answer": [0],
    "explanation": "Explanation text",
    "pitfall": "Common pitfall information",
    "difficulty": "easy"
  }
]
```

## Question Object Properties

### Required Properties

1. **id** (number): A unique identifier for the question
2. **question** (string): The question text
3. **options** (array of strings): The answer options
4. **answer** (array of numbers): The correct answer indices (0-based)

### Optional Properties

1. **link** (string): URL to the original source page of the question. When provided, a **"Xem câu hỏi gốc"** button appears in the question header allowing users to visit the original source. The app accepts **any valid URL** — there is no domain filter in code. As a project convention, only questions from these validated sources should be used:
   - `https://www.examveda.com/`
   - `https://ocpjp.jobs4times.com/`
   - `https://www.indiabix.com/java-programming`
   - `https://www.sanfoundry.com/java-questions-answers-freshers-experienced/`
2. **explanation** (string): Detailed explanation of the correct answer(s). Appears when user answers correctly, or after clicking "Xem Lời Giải" if answered incorrectly
3. **pitfall** (string): Information about common mistakes or misconceptions
4. **difficulty** (string): Difficulty level (`"easy"`, `"medium"`, `"hard"`) — stored in memory but **not currently displayed in the UI**; defaults to `"medium"`

## Special Formatting Features

The application supports special formatting that enhances the presentation of technical content.

### Code Blocks (Java, SQL, etc.)

To include code in your questions, options, explanations, or pitfalls, wrap the code with triple backticks followed by the language identifier. The application will render these in a premium macOS-style window container with:
- Thick left-border accent
- Syntax highlighting (for `java` and `sql` only — other languages get an icon but no coloring)
- Copy button
- Language badge with icon at the bottom-right

Supported language identifiers:
| Identifier | Syntax Highlight | Icon |
|-----------|-----------------|------|
| `java` | Yes (keywords, types, strings, numbers, comments) | Java coffee cup |
| `sql` | Yes (keywords, strings, numbers, comments) | Database icon |
| `javascript` / `js` | No (icon only) | JS icon |
| `html` | No (icon only) | HTML5 icon |
| `css` | No (icon only) | CSS3 icon |
| *(anything else)* | No | Generic code icon |

```markdown
```java
public class App {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
```
```

Example in a question:
```json
{
  "id": 1,
  "link": "https://www.examveda.com/...",
  "question": "What is the output of this Java code snippet? ```java System.out.println(10 + 20 + \"Java\"); ```",
  "options": [
    "30Java",
    "1020Java",
    "Java30",
    "Compilation fails"
  ],
  "answer": [0],
  "explanation": "The expression is evaluated from left to right. First, `10 + 20` is evaluated as integer addition resulting in `30`. Then, `30 + \"Java\"` performs string concatenation, resulting in `30Java`.",
  "pitfall": "Common pitfall: assuming string concatenation happens first, which would result in `1020Java`.",
  "difficulty": "medium"
}
```

### Bold Text Formatting

To emphasize text, wrap it with double asterisks. Bold text renders in uppercase with the primary color:

```markdown
**This text will be bold and uppercase**
```

### Inline Code Formatting

To highlight technical terms or code snippets inline, wrap them with single backticks:

```markdown
Use the `SELECT` statement to retrieve data
```

## UI Behavior

### "Xem câu hỏi gốc" (View Original Question) Button

- Appears in the **top-right of the question header**, next to the question number badge
- Only visible when the question has a non-empty `"link"` field
- Opens the original source URL in a **new browser tab**
- Styled as an elegant pill-shaped button with hover animation

### "Xem Lời Giải" (View Explanation) Button

- Appears **only when the user answers incorrectly**
- Requires a manual click to reveal the explanation and pitfall sections
- If the user answers correctly, explanation is shown automatically
- After quiz submission, all explanations are visible during review

### Answer Marking Logic

- **Correct answers**: highlighted in green
- **Incorrect selected answers**: highlighted in red
- **Multiple-answer questions**: badge "M" appears on the question number; user selects multiple options
- Sidebar question list shows color-coded status (current/answered/correct/incorrect)

## Complete Example

```json
[
  {
    "id": 1,
    "question": "Which of this method can be used to make the main thread to be executed last among all the threads?",
    "link": "https://www.examveda.com/which-of-this-method-can-be-used-to-make-the-main-thread-to-be-executed-last-among-all-the-threads-222232/",
    "options": [
      "stop()",
      "sleep()",
      "join()",
      "call()"
    ],
    "answer": [2],
    "explanation": "**LỜI GIẢI CHI TIẾT**\n\n`join()` is used to wait for a thread to finish execution before the current thread continues.",
    "pitfall": "**LƯU Ý / PITFALL**\n\nSai lầm phổ biến: nhầm `sleep()` với `join()` vì cả hai đều làm thread hiện tại tạm dừng.",
    "difficulty": "easy"
  },
  {
    "id": 2,
    "question": "Which three statements are true about Java threads? (Choose three.)",
    "link": "https://ocpjp.jobs4times.com/multiThread.html",
    "options": [
      "A thread can be started more than once",
      "Threads share the same heap memory",
      "Each thread has its own stack",
      "The `synchronized` keyword prevents deadlock",
      "A thread enters the blocked state when waiting for a monitor lock"
    ],
    "answer": [1, 2, 4],
    "explanation": "**B**, **C**, and **E** are correct. Threads share heap memory but each has its own stack. A thread enters the blocked state when waiting for a monitor lock.",
    "pitfall": "Common pitfall: confusing blocked and waiting states, and assuming `synchronized` prevents deadlock — it does not.",
    "difficulty": "hard"
  }
]
```

## Best Practices

1. **Use descriptive IDs**: Sequential IDs help with organization and link mapping
2. **Always include a validated link**: Use only the 4 accepted source domains listed above
3. **Clear questions**: Make sure your questions are unambiguous
4. **Plausible distractors**: Incorrect options should be reasonable to prevent guessing
5. **Comprehensive explanations**: Start with a short summary, then analyze each option (A/B/C/D)
6. **Meaningful pitfalls**: Identify the most common mistake related to the question topic
7. **No icons in text**: Do not use emoji or special icons (✅, ❌, etc.) in explanation or pitfall text
8. **Consistent formatting**: Use `**bold**` for headers and key terms, backtick for inline code
9. **Test your JSON**: Ensure your JSON is valid before importing

## Testing Your Questions

1. Validate your JSON structure using a JSON validator
2. Import into Pro Quiz Master by uploading the `.json` file or pasting in the text area
3. Verify that code blocks display with syntax highlighting
4. Click "Xem câu hỏi gốc" to confirm the link opens correctly
5. Answer a question incorrectly to verify the "Xem Lời Giải" button appears
6. Confirm that multiple-answer questions show the "M" badge

## Troubleshooting

If your questions don't import correctly:

1. Check for JSON syntax errors (missing commas, unmatched brackets, etc.)
2. Ensure all text is properly escaped (especially backslashes `\\` and quotes `\"` within strings)
3. Verify that answer indices match the options array (0-based indexing)
4. Make sure code blocks use the correct ` ```java ` or ` ```sql ` syntax
5. Confirm the `"link"` field uses a full URL starting with `https://`
6. The app has auto-fix for common backslash escaping issues — check the browser console for messages

By following this tutorial, you can create engaging, well-formatted technical quizzes that take full advantage of the Pro Quiz Master application's features.
