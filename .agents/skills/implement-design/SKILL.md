---
name: implement-design
description: >-
  Triggered by the command 'impl-design <feature/screen name>' or '/impl-design <feature/screen name>'.
  Use this skill whenever implementing, building, or updating Android UI screens, features,
  or components from Figma in the ShaterAI project. Enforces modern Kotlin Jetpack Compose,
  Clean Architecture, Domain-Driven Design (DDD), Model-View-Intent (MVI), Figma visual fidelity,
  and strict component reuse.
---

# Implement Design Skill (ShaterAI)

This skill guides the implementation of UI screens and features from the **SHATER AI** Figma design into modern, idiomatic Android Kotlin code using Jetpack Compose, Clean Architecture, DDD, and MVI.

## Command Invocation

```text
impl-design <feature/screen name>
/impl-design <feature/screen name>
```

* **Example:** `impl-design meetings-list` or `/impl-design meetings-list-light-en`
* When triggered, extracts the screen/feature name, inspects the Figma design node, audits reusable UI components, and builds the full Clean Architecture + DDD + MVI stack.


---

## 1. Project & Figma Source of Truth

* **Figma File URL:** [SHATER AI Figma Design](https://www.figma.com/design/3zX7JFMMuzyVcLBkdHgjAh/SHATER-AI?node-id=3-13)
* **File Key:** `3zX7JFMMuzyVcLBkdHgjAh`
* **Reference Screen:** `meetings-list-light-en` (`3:13`)
* **Design Tokens Reference:** Read [design-tokens.md](./references/design-tokens.md) for exact hex colors, typography scales, corner radiuses, and spacing.
* **Architecture Template Reference:** Read [mvi-clean-arch-template.md](./references/mvi-clean-arch-template.md) for boilerplate contracts and ViewModel structure.

---

## 2. Core Architecture: Feature-First + Clean Architecture + DDD

Every screen represents a distinct **Feature**. Code must be packaged **feature-first** under `me.dwidar.shaterai.feature.<feature_name>/` using DDD layering:

```text
me.dwidar.shaterai.feature.<feature_name>/
├── domain/                         # Domain Layer (Pure Kotlin, zero Android framework imports)
│   ├── model/                      # Domain Entities, Value Objects
│   │   └── Meeting.kt
│   ├── repository/                 # Repository Interface (DDD Port)
│   │   └── MeetingsRepository.kt
│   └── usecase/                    # Domain Use Cases (Single responsibility business actions)
│       ├── GetRecentMeetingsUseCase.kt
│       └── SearchMeetingsUseCase.kt
├── data/                           # Data Layer
│   ├── repository/                 # Repository Implementation
│   │   └── MeetingsRepositoryImpl.kt
│   ├── datasource/                 # Remote / Local data sources
│   │   └── MeetingsLocalDataSource.kt
│   └── mapper/                     # DTO / Entity to Domain Model Mappers
│       └── MeetingMappers.kt
└── presentation/                   # Presentation Layer (Jetpack Compose + AndroidX ViewModel)
    ├── mvi/
    │   ├── <Feature>Contract.kt    # State, Intent, Effect definitions
    │   └── <Feature>ViewModel.kt   # MVI StateFlow container
    └── ui/
        ├── <Feature>Screen.kt      # Root composable with Scaffold / layout
        └── components/             # Feature-specific sub-composables
            └── MeetingCard.kt
```

### 2.1 Reusable UI Components & Pre-Modular Structure (`ui_kit_feature`)

To prepare for a multi-modular architecture (e.g., `:core:ui-kit` / `:feature:ui-kit`) without introducing complex Gradle multi-modules right now:
* **Dedicated `ui_kit_feature` Package:**
  * Any UI component that is reusable and shared across multiple features or screens MUST be placed in `me.dwidar.shaterai.feature.ui_kit_feature/`.
  * Package structure for `ui_kit_feature`:
    ```text
    me.dwidar.shaterai.feature.ui_kit_feature/
    ├── components/
    │   ├── ShaterTopAppBar.kt
    │   ├── ShaterSearchBar.kt
    │   ├── ShaterFilterButton.kt
    │   ├── ShaterBottomNavigationBar.kt
    │   ├── ShaterFloatingActionButton.kt
    │   ├── ShaterStatusDot.kt
    │   └── ShaterUserAvatar.kt
    └── theme/
        ├── ShaterColors.kt
        ├── ShaterTypography.kt
        └── ShaterElevation.kt
    ```
* **Strict Component Reuse:**
  * **DO NOT** duplicate shared components across individual feature packages.
  * Always check `ui_kit_feature` first. If a component already exists or can be generalized, reuse or extend it.

### 2.2 Strict Component Isolation: One UI Component Per File
* **Every UI component MUST be placed in its own dedicated, separate file.**
* Never define multiple UI components in the same Kotlin file or inline at the bottom of a `Screen.kt` file.
* Each component file must contain:
  1. The single `@Composable` component function.
  2. Any component-specific internal sub-elements or helper modifiers.
  3. A dedicated `@Preview(showBackground = true)` preview function wrapped in `ShaterAITheme`.
* File naming must match the component name (e.g. `MeetingCard.kt` for `@Composable fun MeetingCard(...)`).

---

## 3. MVI (Model-View-Intent) Architectural Rules

Every feature must follow the unidirectional data flow (UDF) of MVI:

1. **Immutable State (`<Feature>State`)**:
   * Single `data class` representing the entire UI state.
   * Provide a sensible `initial()` factory or default values.
   * Mark with `@Immutable` or `@Stable` for Compose compiler optimizations.
2. **User Intents (`<Feature>Intent`)**:
   * `sealed interface` representing every user gesture, action, or lifecycle event.
   * Example: `OnSearchQueryChanged(val query: String)`, `OnMeetingClicked(val id: String)`, `OnRecordClicked`.
3. **One-Off Side Effects (`<Feature>Effect`)**:
   * `sealed interface` for transient actions that shouldn't survive configuration changes.
   * Example: `NavigateToRecording`, `NavigateToDetails(val id: String)`, `ShowSnackbar(val message: String)`.
   * Managed via `Channel<Effect>(Channel.BUFFERED)` and exposed as `Flow<Effect>`.
4. **ViewModel (`<Feature>ViewModel`)**:
   * Extends `androidx.lifecycle.ViewModel`.
   * Holds `_uiState = MutableStateFlow(<Feature>State())` and exposes `uiState: StateFlow<FeatureState>`.
   * Exposes a single public entry point: `fun onIntent(intent: <Feature>Intent)`.
   * Coordinates Domain Use Cases inside `viewModelScope`.

---

## 4. Modern Android & Jetpack Compose Standards

* **No Deprecated APIs**:
  * Use Material3 (`androidx.compose.material3.*`).
  * Use `collectAsStateWithLifecycle()` from `androidx.lifecycle.compose` to collect flows safely.
  * Use `Modifier.fillMaxSize()`, `Modifier.padding()`, and Material 3 padding values.
* **Edge-to-Edge System Bar Handling**:
  * Respect system insets using `Modifier.statusBarsPadding()`, `Modifier.navigationBarsPadding()`, or passing inner padding from `Scaffold`.
* **Typography & Fonts**:
  * Brand titles use `Outfit` font family (`FontWeight.ExtraBold` or `FontWeight.Bold`).
  * Body and labels use `Geist` or platform clean sans-serif.
* **Previews**:
  * Every composable screen and sub-component must provide a `@Preview(showBackground = true)` composable wrapped in `ShaterAITheme`.

---

## 5. Workflow: Step-by-Step Design Implementation

When assigned to implement or update a screen from Figma:

1. **Inspect Figma Metadata & Screenshot**:
   * Call `get_metadata` or `get_design_context` using `fileKey: "3zX7JFMMuzyVcLBkdHgjAh"` and the target `nodeId` (e.g. `3:13` for meetings list).
   * Verify the exact visual target against the Figma screenshot.
2. **Audit Existing Components & UI Kit**:
   * Search `me/dwidar/shaterai/feature/ui_kit_feature/` and `feature/` for existing search bars, cards, buttons, or navigation components.
   * If a matching component exists, reuse or parameterize it. Avoid duplicate UI code.
   * If designing a reusable component (e.g. general button, search bar, top bar, nav bar), add it directly to `ui_kit_feature/components/` in its own separate file.
3. **Build the Domain Layer (DDD)**:
   * Define the entity models in `feature/<name>/domain/model/`.
   * Define repository interface in `feature/<name>/domain/repository/`.
   * Implement single-responsibility use cases in `feature/<name>/domain/usecase/`.
4. **Build the Presentation MVI Layer**:
   * Define State, Intent, and Effect in `<Feature>Contract.kt`.
   * Implement `<Feature>ViewModel.kt` handling intents and emitting state.
5. **Implement Compose UI**:
   * Create `<Feature>Screen.kt` using Scaffold, LazyColumn/LazyRow for scrolling lists, and exact Figma colors and shapes.
   * Extract every sub-component into its own separate file under `ui/components/` (or `ui_kit_feature/components/` if shared). Never bundle multiple components into one file.
6. **Verify Visual Fidelity**:
   * Compare margins, paddings, corner radiuses (e.g. 16.dp for cards, 24.dp for search bar, 16.dp for FAB).
   * Ensure light/dark theme adaptability.
