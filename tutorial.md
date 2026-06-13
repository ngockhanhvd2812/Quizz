# Tutorial: Creating Quiz Questions for the Pro Quiz Master Application

This tutorial explains how to create quiz questions in the correct format for the DBA Arena / Pro Quiz Master application. The application primarily focuses on SQL and Database Administration questions, but also supports Java coding questions. It uses a specific JSON structure and supports special formatting features including SQL/Java code blocks and markdown-like syntax.

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

1. **link** (string): URL to the original source page of the question. When provided, a **"Xem câu hỏi gốc"** button appears in the question header allowing users to visit the original source. The app accepts **any valid URL** — there is no domain filter in code. As a project convention, only questions from validated sources should be used, such as official database documentation or programming tutorials:
   - `https://docs.oracle.com/` (Oracle Documentation)
   - `https://www.postgresql.org/docs/` (PostgreSQL Documentation)
   - `https://www.examveda.com/` (Examveda Quiz Site)
   - `https://ocpjp.jobs4times.com/` (Java OCP Quiz Site - for Java questions)
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
```sql
SELECT employee_id, first_name, salary 
FROM employees 
WHERE salary > 5000 
ORDER BY salary DESC;
```
*(Note: Java is also fully supported using ` ```java ` block formatting).*

Example of an SQL question:
```json
{
  "id": 1,
  "link": "https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/STARTUP.html",
  "question": "Bạn cần khởi động Oracle 19c đến trạng thái **NOMOUNT** để restore control file. Chọn lệnh đúng để chạy trong SQL*Plus.",
  "options": [
    "```sql\nSTARTUP NOMOUNT;\n```",
    "```sql\nSTARTUP MOUNT;\n```",
    "```sql\nALTER DATABASE NOMOUNT;\n```",
    "```sql\nALTER SYSTEM NOMOUNT;\n```"
  ],
  "answer": [0],
  "explanation": "**Tóm tắt yêu cầu:** Chọn lệnh đưa database instance lên NOMOUNT.\n\n**Bước 1 - Giải thích ngắn gọn:** Lệnh `STARTUP NOMOUNT` khởi tạo instance, đọc parameter file (SPFILE/PFILE) và cấp phát bộ nhớ (SGA), nhưng chưa đọc control file.\n\n**Bước 2 - Điểm dễ nhầm khi đọc đề:**\n- Nhầm `NOMOUNT` với `MOUNT`: Trạng thái `MOUNT` yêu cầu control file đã được mở và đọc.\n- Sử dụng `ALTER DATABASE` khi database đang shutdown: Lệnh này chỉ hoạt động khi instance đã chạy.\n\n**Bước 3 - Phân tích từng đáp án:**\n- **A**: Đúng. Lệnh chuẩn để tạo instance không cần mount database.\n- **B**: Sai. Lệnh này mount database bằng cách đọc control file.\n- **C** và **D**: Sai cú pháp khởi động từ trạng thái tắt.",
  "pitfall": "Sai lầm phổ biến: Nhầm lẫn giữa trạng thái `NOMOUNT` (chỉ khởi tạo bộ nhớ SGA/background processes) và `MOUNT` (đọc control file để liên kết database). Để khôi phục control file bị mất, ta bắt buộc phải vào `NOMOUNT`.",
  "difficulty": "easy"
}
```
*(Note: You can write the questions in English or Vietnamese depending on your audience).*

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
    "question": "Which shutdown mode in Oracle Database 19c rolls back active transactions, disconnects users, and shuts down the database cleanly without requiring recovery on the next startup?",
    "link": "https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/SHUTDOWN.html",
    "options": [
      "```sql\nSHUTDOWN NORMAL;\n```",
      "```sql\nSHUTDOWN IMMEDIATE;\n```",
      "```sql\nSHUTDOWN ABORT;\n```",
      "```sql\nSHUTDOWN TRANSACTIONAL;\n```"
    ],
    "answer": [1],
    "explanation": "**LỜI GIẢI CHI TIẾT**\n\n`SHUTDOWN IMMEDIATE` is the standard shutdown mode for DBA maintenance. It prevents new connections, terminates active sessions, rolls back uncommitted transactions, and shuts down the database cleanly.",
    "pitfall": "**LƯU Ý / PITFALL**\n\n- Do not confuse `IMMEDIATE` with `ABORT`. `ABORT` is a dirty shutdown (equivalent to pulling the power plug) and will require instance recovery next startup.\n- Confusing `IMMEDIATE` with `NORMAL` (which waits indefinitely for users to disconnect) or `TRANSACTIONAL` (which waits for current transactions to finish).",
    "difficulty": "easy"
  },
  {
    "id": 2,
    "question": "Which statements are true about Java threads? (Choose all correct options — Java is also supported by the app)",
    "link": "https://ocpjp.jobs4times.com/multiThread.html",
    "options": [
      "```java\n// Option 1\nthread.start();\nthread.start(); // throws IllegalThreadStateException\n```",
      "Threads share the same heap memory",
      "Each thread has its own private stack frame",
      "The `synchronized` keyword prevents deadlocks automatically"
    ],
    "answer": [0, 1, 2],
    "explanation": "**EXPLANATION**\n\n- Attempting to start a thread that has already started will throw `IllegalThreadStateException`.\n- Threads share the same heap memory segment but have their own stacks.\n- `synchronized` is used for mutual exclusion, it does not prevent deadlocks (it can actually cause deadlocks if locking order is incorrect).",
    "pitfall": "Common misconception: assuming `synchronized` prevents deadlock, or that a thread can be restarted.",
    "difficulty": "medium"
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
