# Tutorial: Creating Quiz Questions for the Pro Quiz Master Application

This tutorial explains how to create quiz questions in the correct format for the Pro Quiz Master application. The application uses a specific JSON structure and supports special formatting features including SQL code blocks and markdown-like syntax.

## JSON Structure Overview

Each quiz is a JSON array containing question objects. Here's the basic structure:

```json
[
  {
    "id": 1,
    "question": "Your question text here",
    "options": [
      "Option A",
      "Option B",
      "Option C",
      "Option D"
    ],
    "answer": [0, 1],
    "explanation": "Explanation text",
    "pitfall": "Common pitfall information"
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

1. **explanation** (string): Detailed explanation of the correct answer(s)
2. **pitfall** (string): Information about common mistakes or misconceptions
3. **difficulty** (string): Difficulty level ("easy", "medium", "hard") - defaults to "medium"

## Special Formatting Features

The application supports special formatting that enhances the presentation of technical content.

### Code Blocks (Java, SQL, etc.)

To include code in your questions, options, explanations, or pitfalls, wrap the code with triple backticks followed by the programming language (e.g., `java`, `sql`, `javascript`). The application will render these in a premium, macOS-style window container with a prominent highlighted border (including a thick left border accent), custom syntax highlighting, and a language indicator badge with a representative icon (e.g. Java coffee cup) at the bottom-right corner:

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
  "question": "What is the output of this Java code snippet? ```java System.out.println(10 + 20 + \"Java\"); ```",
  "options": [
    "30Java",
    "1020Java",
    "Java30",
    "Compilation fails"
  ],
  "answer": [0],
  "explanation": "The expression is evaluated from left to right. First, `10 + 20` is evaluated as integer addition resulting in `30`. Then, `30 + \"Java\"` performs string concatenation, resulting in `30Java`.",
  "pitfall": "Common pitfall: assuming string concatenation happens first, which would result in `1020Java`."
}
```

### Bold Text Formatting

To emphasize text with bold formatting, wrap it with double asterisks:

```markdown
**This text will be bold and uppercase**
```

Example:
```json
{
  "id": 2,
  "question": "Which **SQL** keyword is used to sort results?",
  "options": [
    "**ORDER BY**",
    "**SORT**",
    "**ARRANGE**",
    "**SEQUENCE**"
  ],
  "answer": [0],
  "explanation": "The **ORDER BY** clause is used to sort the result set in ascending or descending order.",
  "pitfall": "Common pitfall: confusing **ORDER BY** with **GROUP BY**, which is used for grouping rows rather than sorting."
}
```

### Inline Code Formatting

To highlight technical terms or code snippets inline, wrap them with single backticks:

```markdown
Use the `SELECT` statement to retrieve data
```

Example:
```json
{
  "id": 3,
  "question": "Which SQL command retrieves data from a database?",
  "options": [
    "`GET`",
    "`FETCH`",
    "`SELECT`",
    "`RETRIEVE`"
  ],
  "answer": [2],
  "explanation": "The `SELECT` statement is the standard SQL command for querying data.",
  "pitfall": "Common pitfall: using `GET` which is not a standard SQL command for data retrieval."
}
```

## Complete Example

Here's a complete example showing all formatting features:

```json
[
  {
    "id": 1,
    "question": "What will be the result of executing this **SQL** query? ```sql CREATE TABLE employees (id NUMBER, name VARCHAR2(50)); ```",
    "options": [
      "Creates a table named `employees`",
      "Deletes the `employees` table",
      "Updates the `employees` table",
      "Selects data from `employees` table"
    ],
    "answer": [0],
    "explanation": "The **CREATE TABLE** statement creates a new table. In this case, it creates an `employees` table with two columns: `id` of type NUMBER and `name` of type VARCHAR2(50).",
    "pitfall": "Common pitfall: forgetting the column definitions which would cause a syntax error. The correct syntax requires specifying column names and data types."
  },
  {
    "id": 2,
    "question": "Which three statements are true about **UNDO** tablespaces? (Choose three.)",
    "options": [
      "**UNDO** retention guarantees are never overridden",
      "Active **UNDO** is always retained",
      "**UNDO** segments are automatically managed",
      "Multiple **UNDO** tablespaces can be active simultaneously",
      "An instance crash occurs if the active **UNDO** tablespace is lost"
    ],
    "answer": [1, 2, 4],
    "explanation": "Correct answers are: **B**, **C**, and **E**. Active **UNDO** is always retained to maintain transaction consistency. **UNDO** segments are automatically managed by Oracle. If the active **UNDO** tablespace is lost, the instance will crash to protect data integrity.",
    "pitfall": "Common pitfall: selecting option **A**. **UNDO** retention guarantees can be overridden in emergency situations to prevent transaction failures."
  }
]
```

## Best Practices

1. **Use descriptive IDs**: While not strictly required, using sequential IDs helps with organization
2. **Clear questions**: Make sure your questions are unambiguous
3. **Plausible distractors**: Incorrect options should be reasonable to prevent guessing
4. **Comprehensive explanations**: Provide detailed explanations that help users understand the concept
5. **Meaningful pitfalls**: Identify common mistakes or misconceptions that users should avoid
6. **Consistent formatting**: Use the formatting features consistently throughout your quiz
7. **Test your JSON**: Ensure your JSON is valid before importing into the application

## Testing Your Questions

1. Validate your JSON structure using a JSON validator
2. Test the import in the Pro Quiz Master application
3. Verify that all formatting (SQL code blocks, bold text, inline code) displays correctly
4. Check that explanations and pitfalls appear when reviewing questions
5. Confirm that the correct answers are properly marked

## Troubleshooting

If your questions don't import correctly:

1. Check for JSON syntax errors (missing commas, unmatched brackets, etc.)
2. Ensure all text is properly escaped (especially quotes within strings)
3. Verify that answer indices match the options array (0-based indexing)
4. Make sure SQL code blocks use the correct ```sql syntax
5. Confirm that markdown formatting uses the correct **bold** and `inline` syntax

By following this tutorial, you can create engaging, well-formatted technical quizzes that take full advantage of the Pro Quiz Master application's features.
