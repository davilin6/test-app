# Quiz CLI

An interactive, terminal-based programming quiz written in modern JavaScript.

## Project Overview

Quiz CLI is a dependency-free Node.js learning game. It loads category-based multiple-choice questions from a local JSON file, lets players choose a category and quiz length, grades each answer immediately, and presents a summary with missed-question review at the end.

### Features

- Three built-in categories: **JavaScript Basics**, **Node.js Fundamentals**, and **General Programming**.
- Five questions per category (15 questions total in the included dataset).
- Choice of all available questions, three questions, or five questions when supported by the selected category.
- Randomized question order for every quiz session.
- Validated numeric menu input and yes/no replay prompt.
- Immediate correct/incorrect feedback with explanations.
- Progress bar, final percentage, performance message, and review of incorrect answers.
- ANSI-colored terminal output implemented without third-party packages.

## Requirements

- [Node.js](https://nodejs.org/) **18.0.0 or later** (as declared in `package.json`).
- npm, included with standard Node.js installations.
- An interactive terminal that supports standard input/output and ANSI escape sequences for color.

## Installation and Setup

1. Clone the repository:

   ```bash
   git clone https://github.com/davilin6/test-app.git
   ```

2. Enter the application directory:

   ```bash
   cd test-app/test-app
   ```

3. Install project metadata (optional):

   ```bash
   npm install
   ```

   The application declares no external runtime dependencies, so it can also be run immediately after cloning with Node.js.

## Usage

Start an interactive quiz:

```bash
npm start
```

Equivalent direct invocation:

```bash
node index.js
```

When prompted, enter the displayed number to choose a category, quiz length, and answer. Press Enter between questions, then answer the replay prompt with `y`/`yes` to start another round or any other response to exit.

### Example Session

```text
Choose a category:

  1. JavaScript Basics
  2. Node.js Fundamentals
  3. General Programming

Your choice (enter number): 1

How many questions?

  1. All questions
  2. 3 questions
  3. 5 questions

Your choice (enter number): 2
```

Each answer is evaluated as soon as it is submitted. The final screen shows the category, score, percentage, a performance message, and—when applicable—the player’s answer and correct answer for every missed question.

## Scripts

| Command | Description |
| --- | --- |
| `npm start` | Runs the interactive CLI through `node index.js`. |
| `npm test` | Runs Node.js's built-in test runner (`node --test`). No test files are currently included. |

## Question Content

Questions are stored in `data/questions.json`. The supplied dataset has three category IDs:

| ID | Display name | Included questions |
| --- | --- | ---: |
| `javascript` | JavaScript Basics | 5 |
| `nodejs` | Node.js Fundamentals | 5 |
| `general` | General Programming | 5 |

Every question follows this structure:

```json
{
  "question": "Question text",
  "options": ["Option 1", "Option 2", "Option 3", "Option 4"],
  "answer": 0,
  "explanation": "Explanation shown after the answer"
}
```

`answer` is a zero-based index into `options`. Keep it aligned with the intended correct option when adding or editing questions. The CLI derives category choices directly from `categories`, so new categories in this file become selectable without changes to the application code.

## Architecture and Runtime Flow

1. `index.js` resolves its own directory from `import.meta.url`, reads `data/questions.json` with `node:fs/promises`, and creates a `readline` interface.
2. The player selects a category and a supported question count. Question limits are based on the chosen category’s available questions.
3. `Quiz` receives the selected questions, copies and randomizes them with the Fisher–Yates algorithm, then manages progress, score, and answer history.
4. `src/input.js` wraps `readline` callbacks in Promises for `async`/`await`-based prompts and validates menu selections.
5. `Quiz.askQuestion()` renders a progress bar, collects an answer, records the result, and displays the applicable explanation.
6. `Quiz.showResults()` calculates the final percentage, selects a performance message, and lists all incorrect answers for review.
7. The application asks whether to begin a new quiz and always closes the readline interface in `finally`; unexpected failures print an error and stack trace before exiting with status code `1`.

## File Structure

```text
.
└── test-app/
    ├── README.md              # Project documentation
    ├── index.js               # CLI entry point and application loop
    ├── package.json           # Package metadata, Node version, and npm scripts
    ├── data/
    │   └── questions.json     # Categories and multiple-choice question data
    └── src/
        ├── colors.js          # ANSI styling helpers
        ├── input.js           # Promise-based terminal input utilities
        └── quiz.js            # Quiz state, question flow, scoring, and results
```
