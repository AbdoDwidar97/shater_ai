# Workspace Commands & Skill Dispatcher

This document defines the custom commands supported in this workspace and how they map to specialized agent skills. Future commands and skills must be registered here.

---

## Command Registry

| Command | Mapped Skill | Description / Arguments |
| :--- | :--- | :--- |
| `git-commit-msg`<br>`/git-commit-msg` | [`git-commit-msg`](../skills/git-commit-msg/SKILL.md) | Generates a structured Git commit message based on workspace git status and diff. |
| `impl-design <feature/screen name>`<br>`/impl-design <feature/screen name>` | [`implement-design`](../skills/implement-design/SKILL.md) | Implements an Android UI screen or feature from Figma using Jetpack Compose, Clean Architecture, DDD, and MVI. |

---

## 1. Command: `git-commit-msg`

### Syntax
```text
git-commit-msg
/git-commit-msg
git-commit-msg [additional context / notes]
```

### Examples
- `git-commit-msg`
- `/git-commit-msg`
- `git-commit-msg added meeting details screen and use case`

### Execution Directives
When this command is invoked:
1. **Activate Skill**:
   - Immediately load and read [`.agents/skills/git-commit-msg/SKILL.md`](../skills/git-commit-msg/SKILL.md).
2. **Inspect Git Changes**:
   - Run `git diff --cached` to review staged changes.
   - Run `git diff` to review unstaged changes.
   - Run `git status --short` to see modified and untracked files.
   - Incorporate any user-provided notes or summary context.
3. **Format strictly according to Rules**:
   - Section 1: **Change Overview Title** (Title Case, no type prefix like `feat:`, concise one line).
   - Section 2: **Change Description and Details** (concise single paragraph in natural professional English explaining what and why).
   - Section 3: **Commit Datetime** (`Commit datetime: DD Mon YYYY - hh:mmAM/PM` with current date and time).
   - Strict Constraints: No Markdown headings, no emojis, no wrapping quotes, and no surrounding commentary before or after.

---

## 2. Command: `impl-design`

### Syntax
```text
impl-design <feature/screen name>
/impl-design <feature/screen name>
```

### Examples
- `impl-design meetings-list`
- `/impl-design meetings-list-light-en`
- `impl-design recording-screen`

### Execution Directives
When this command is invoked:
1. **Activate Skill**:
   - Immediately load and read [`.agents/skills/implement-design/SKILL.md`](../skills/implement-design/SKILL.md) using `view_file`.
   - Read token definitions in [`.agents/skills/implement-design/references/design-tokens.md`](../skills/implement-design/references/design-tokens.md) and architecture templates in [`.agents/skills/implement-design/references/mvi-clean-arch-template.md`](../skills/implement-design/references/mvi-clean-arch-template.md) as needed.
2. **Extract Target**:
   - Identify the screen or feature name from the command argument (`<feature/screen name>`).
3. **Figma Lookup & Inspection**:
   - Use the Figma MCP tools (`get_metadata`, `get_design_context`, `get_screenshot`) with file key `3zX7JFMMuzyVcLBkdHgjAh` to inspect the exact design node corresponding to the screen.
4. **Follow 6-Step Implementation**:
   - **Step 1: Inspect Figma** - Review metadata, layout bounds, typography, and visual assets.
   - **Step 2: Audit UI Kit (`ui_kit_feature`)** - Check for existing shared components in `me.dwidar.shaterai.feature.ui_kit_feature/components/`. Reuse or extend existing components; avoid duplicating code.
   - **Step 3: Domain Layer (DDD)** - Build pure Kotlin models, repository interfaces, and use cases in `me.dwidar.shaterai.feature.<feature>.domain`.
   - **Step 4: Presentation Layer (MVI)** - Define `<Feature>State`, `<Feature>Intent`, `<Feature>Effect`, and `<Feature>ViewModel` in `presentation.mvi`.
   - **Step 5: Compose UI** - Build `<Feature>Screen.kt` and sub-components under `ui/components/`. **Rule:** Strictly one UI component per file with `@Preview`.
   - **Step 6: Visual Fidelity** - Ensure design tokens, spacing, colors, font families (Outfit ExtraBold / Geist), and dark/light support match Figma exactly.

---

## 3. Framework for Future Skills & Commands

When building and registering new skills in the future, adhere to the following protocol:

1. **Create Skill Directory**:
   - Add `.agents/skills/<skill-name>/SKILL.md`.
   - Ensure the YAML frontmatter includes:
     ```yaml
     ---
     name: <skill-name>
     description: >-
       Triggered by the command '<command-name> <args>' or '/<command-name> <args>'.
       Use this skill whenever...
     ---
     ```
2. **Register Command in this Document**:
   - Add a row to the **Command Registry** table above.
   - Add a dedicated specification section with syntax, arguments, examples, and execution directives.
3. **Register in IDE Global Rules (if applicable)**:
   - If the command should also be recognized globally across all projects, add its entry to `~/.gemini/GEMINI.md`.
