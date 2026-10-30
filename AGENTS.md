# ShaterAI Agent Guidelines & Commands

Welcome to the **ShaterAI** Android project. This repository uses the Antigravity Agent Customization System to maintain high architectural fidelity, visual alignment with Figma, and strict Clean Architecture + DDD + MVI standards.

---

## Commands & Skills

The workspace supports specialized commands that route directly to procedural skills:

| Command | Skill | Description |
| :--- | :--- | :--- |
| `impl-design <feature/screen name>`<br>`/impl-design <feature/screen name>` | [`implement-design`](.agents/skills/implement-design/SKILL.md) | Implements an Android UI screen or feature from Figma using Jetpack Compose, Clean Architecture, DDD, and MVI. |

For detailed command specifications and guidelines for adding future commands, see [`.agents/rules/commands.md`](.agents/rules/commands.md).

---

## Core Architecture Principles

1. **Feature-First + DDD**:
   - Every feature lives under `me.dwidar.shaterai.feature.<feature_name>/` containing `domain/`, `data/`, and `presentation/` layers.
2. **Strict Component Isolation**:
   - **One UI Component Per File**: Every `@Composable` component must reside in its own dedicated Kotlin file with a corresponding `@Preview` function wrapped in `ShaterAITheme`.
   - Never place multiple components in the same file or inline within `<Feature>Screen.kt`.
3. **Strict Component Reuse (`ui_kit_feature`)**:
   - Shared, reusable components (top app bars, search bars, buttons, nav bars, avatar chips) belong in `me.dwidar.shaterai.feature.ui_kit_feature/components/`.
   - Never duplicate shared UI elements across features.
4. **MVI Pattern**:
   - Unidirectional data flow using immutable State, sealed interface Intent, sealed interface Effect, and a ViewModel exposing `StateFlow<State>` and `Flow<Effect>`.
5. **Visual Fidelity**:
   - Adhere strictly to the [SHATER AI Figma Design](https://www.figma.com/design/3zX7JFMMuzyVcLBkdHgjAh/SHATER-AI?node-id=3-13) and design tokens defined in [`.agents/skills/implement-design/references/design-tokens.md`](.agents/skills/implement-design/references/design-tokens.md).
