---
name: git-commit-msg
description: >-
  Triggered by the command 'git-commit-msg' or '/git-commit-msg'.
  Use this skill whenever generating or creating a git commit message based on workspace changes.
---

# Git Commit Message Generator Skill

This skill generates standardized, professional Git commit messages by inspecting workspace changes and strictly formatting the output according to the required 3-section template.

---

## Invocation

The skill is activated when the user inputs:
- `git-commit-msg`
- `/git-commit-msg`
- `git-commit-msg <context/notes>`
- Or requests: "create git commit message", "generate git commit message"

---

## Workflow & Step-by-Step Instructions

1. **Inspect Workspace Git State**:
   - Check staged changes: `git diff --cached`
   - Check unstaged changes: `git diff`
   - Check untracked files and status: `git status --short`
   - If the user provides additional context or notes in their query, incorporate that context alongside the git diff.
   - If no changes are detected, inform the user or request details.

2. **Analyze Changes**:
   - Identify the primary intent and purpose of the changes.
   - Summarize the architectural or functional modifications.
   - Do NOT invent changes that were not made.
   - Group related modifications under a unified theme.

3. **Compute Current Local Datetime**:
   - Retrieve current local date and time.
   - Format must strictly match: `DD Mon YYYY - hh:mmAM/PM`
     - Day of month: 2 digits (e.g., `30`)
     - Month: 3-letter abbreviation (e.g., `Oct`, `Aug`, `Nov`)
     - Year: 4 digits (e.g., `2026`)
     - Time: 12-hour clock with two-digit minute and uppercase AM/PM (e.g., `09:40PM`, `04:15AM`)
     - Example: `Commit datetime: 30 Oct 2026 - 09:55PM`

4. **Strict Output Formatting**:
   The output must match this exact 3-section structure without any markdown formatting, headings, or surrounding commentary:

<Change Overview Title>

<Change Description and Details>

Commit datetime: <DD Mon YYYY - hh:mmAM/PM>

### Rules Checklist:
- **Title**:
  - Title Case (capitalize major words).
  - No prefix like `feat:`, `fix:`, `chore:`, `refactor:`.
  - Concise, ideally one line.
- **Description**:
  - A single concise paragraph in natural, professional English.
  - Explains what was changed and, when relevant, why.
  - Mentions key implementation details.
  - Does NOT unnecessarily enumerate every changed file.
- **Datetime**:
  - Exactly `Commit datetime: DD Mon YYYY - hh:mmAM/PM`.
- **Absolute Constraints**:
  - NO emojis.
  - NO Markdown headings (`#`, `##`, etc.).
  - NO explanations, pleasantries, or preamble before the commit message.
  - NO explanations or postscripts after the commit message.
  - NO quotation marks wrapping the commit message.
