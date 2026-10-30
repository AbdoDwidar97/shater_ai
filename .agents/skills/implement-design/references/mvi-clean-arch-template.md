# MVI + Clean Architecture Template (Jetpack Compose)

This reference contains production-ready Kotlin templates for building features with **Clean Architecture + DDD + MVI** in ShaterAI.

---

## 1. Domain Layer (Pure Kotlin)

### Domain Model: `Meeting.kt`
```kotlin
package me.dwidar.shaterai.feature.meetings.domain.model

data class Meeting(
    val id: String,
    val title: String,
    val formattedDate: String,
    val durationText: String,
    val summaryPreview: String
)
```

### Repository Interface: `MeetingsRepository.kt`
```kotlin
package me.dwidar.shaterai.feature.meetings.domain.repository

import kotlinx.coroutines.flow.Flow
import me.dwidar.shaterai.feature.meetings.domain.model.Meeting

interface MeetingsRepository {
    fun getRecentMeetings(): Flow<List<Meeting>>
    suspend fun searchMeetings(query: String): List<Meeting>
}
```

### Domain Use Case: `GetRecentMeetingsUseCase.kt`
```kotlin
package me.dwidar.shaterai.feature.meetings.domain.usecase

import kotlinx.coroutines.flow.Flow
import me.dwidar.shaterai.feature.meetings.domain.model.Meeting
import me.dwidar.shaterai.feature.meetings.domain.repository.MeetingsRepository

class GetRecentMeetingsUseCase(
    private val repository: MeetingsRepository
) {
    operator fun invoke(): Flow<List<Meeting>> = repository.getRecentMeetings()
}
```

---

## 2. Presentation Layer: MVI Contract

### File: `MeetingsContract.kt`
```kotlin
package me.dwidar.shaterai.feature.meetings.presentation.mvi

import androidx.compose.runtime.Immutable
import me.dwidar.shaterai.feature.meetings.domain.model.Meeting

// 1. Single Immutable UI State
@Immutable
data class MeetingsState(
    val isLoading: Boolean = false,
    val searchQuery: String = "",
    val meetings: List<Meeting> = emptyList(),
    val errorMessage: String? = null
)

// 2. User Intents / Events
sealed interface MeetingsIntent {
    data class OnSearchQueryChanged(val query: String) : MeetingsIntent
    data class OnMeetingClicked(val meetingId: String) : MeetingsIntent
    data object OnRecordFabClicked : MeetingsIntent
    data object OnFilterClicked : MeetingsIntent
    data object OnViewAllClicked : MeetingsIntent
    data object OnThemeToggleClicked : MeetingsIntent
}

// 3. One-off Side Effects
sealed interface MeetingsEffect {
    data class NavigateToMeetingDetails(val meetingId: String) : MeetingsEffect
    data object NavigateToNewRecording : MeetingsEffect
    data class ShowMessage(val message: String) : MeetingsEffect
}
```

---

## 3. Presentation Layer: ViewModel

### File: `MeetingsViewModel.kt`
```kotlin
package me.dwidar.shaterai.feature.meetings.presentation.mvi

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import kotlinx.coroutines.channels.Channel
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.asStateFlow
import kotlinx.coroutines.flow.receiveAsFlow
import kotlinx.coroutines.flow.update
import kotlinx.coroutines.launch
import me.dwidar.shaterai.feature.meetings.domain.usecase.GetRecentMeetingsUseCase

class MeetingsViewModel(
    private val getRecentMeetingsUseCase: GetRecentMeetingsUseCase
) : ViewModel() {

    private val _uiState = MutableStateFlow(MeetingsState())
    val uiState: StateFlow<MeetingsState> = _uiState.asStateFlow()

    private val _effectChannel = Channel<MeetingsEffect>(Channel.BUFFERED)
    val effectFlow = _effectChannel.receiveAsFlow()

    init {
        loadRecentMeetings()
    }

    fun onIntent(intent: MeetingsIntent) {
        when (intent) {
            is MeetingsIntent.OnSearchQueryChanged -> {
                _uiState.update { it.copy(searchQuery = intent.query) }
            }
            is MeetingsIntent.OnMeetingClicked -> {
                sendEffect(MeetingsEffect.NavigateToMeetingDetails(intent.meetingId))
            }
            is MeetingsIntent.OnRecordFabClicked -> {
                sendEffect(MeetingsEffect.NavigateToNewRecording)
            }
            is MeetingsIntent.OnFilterClicked -> {
                sendEffect(MeetingsEffect.ShowMessage("Filter clicked"))
            }
            is MeetingsIntent.OnViewAllClicked -> {
                sendEffect(MeetingsEffect.ShowMessage("View all clicked"))
            }
            is MeetingsIntent.OnThemeToggleClicked -> {
                // Toggle theme logic
            }
        }
    }

    private fun loadRecentMeetings() {
        viewModelScope.launch {
            _uiState.update { it.copy(isLoading = true) }
            getRecentMeetingsUseCase().collect { meetings ->
                _uiState.update { it.copy(isLoading = false, meetings = meetings) }
            }
        }
    }

    private fun sendEffect(effect: MeetingsEffect) {
        viewModelScope.launch {
            _effectChannel.send(effect)
        }
    }
}
```

---

## 4. Presentation Layer: Screen Composable

### File: `MeetingsScreen.kt`
```kotlin
package me.dwidar.shaterai.feature.meetings.presentation.ui

import androidx.compose.foundation.background
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Scaffold
import androidx.compose.runtime.Composable
import androidx.compose.runtime.LaunchedEffect
import androidx.compose.runtime.getValue
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.lifecycle.compose.collectAsStateWithLifecycle
import me.dwidar.shaterai.feature.meetings.presentation.mvi.MeetingsEffect
import me.dwidar.shaterai.feature.meetings.presentation.mvi.MeetingsIntent
import me.dwidar.shaterai.feature.meetings.presentation.mvi.MeetingsState
import me.dwidar.shaterai.feature.meetings.presentation.mvi.MeetingsViewModel

@Composable
fun MeetingsScreen(
    viewModel: MeetingsViewModel,
    onNavigateToDetails: (String) -> Unit,
    onNavigateToRecording: () -> Unit,
    modifier: Modifier = Modifier
) {
    val state by viewModel.uiState.collectAsStateWithLifecycle()

    LaunchedEffect(viewModel) {
        viewModel.effectFlow.collect { effect ->
            when (effect) {
                is MeetingsEffect.NavigateToMeetingDetails -> onNavigateToDetails(effect.meetingId)
                is MeetingsEffect.NavigateToNewRecording -> onNavigateToRecording()
                is MeetingsEffect.ShowMessage -> { /* Show snackbar */ }
            }
        }
    }

    MeetingsContent(
        state = state,
        onIntent = viewModel::onIntent,
        modifier = modifier
    )
}

@Composable
fun MeetingsContent(
    state: MeetingsState,
    onIntent: (MeetingsIntent) -> Unit,
    modifier: Modifier = Modifier
) {
    Scaffold(
        modifier = modifier
            .fillMaxSize()
            .background(Color(0xFFF4F5F9))
    ) { innerPadding ->
        // Render screen sections (Header, Search, MeetingCards list, BottomNav)
    }
}
```

---

## 5. UI Kit Feature & Component Isolation (`ui_kit_feature`)

Reusable components across screens live in `feature/ui_kit_feature/components/`. **Each component must have its own dedicated file and preview.**

### Example Shared Component: `ShaterSearchBar.kt`
*Location: `me.dwidar.shaterai.feature.ui_kit_feature.components.ShaterSearchBar.kt`*
```kotlin
package me.dwidar.shaterai.feature.ui_kit_feature.components

import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import me.dwidar.shaterai.ui.theme.ShaterAITheme

@Composable
fun ShaterSearchBar(
    query: String,
    onQueryChange: (String) -> Unit,
    placeholder: String = "Search meeting summaries...",
    modifier: Modifier = Modifier
) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .height(48.dp)
            .background(Color.White, RoundedCornerShape(24.dp))
            .padding(horizontal = 16.dp),
        verticalAlignment = Alignment.CenterVertically
    ) {
        // Icon and TextField
        Text(
            text = if (query.isEmpty()) placeholder else query,
            color = Color(0xFF79747E),
            fontSize = 15.sp
        )
    }
}

@Preview(showBackground = true)
@Composable
private fun ShaterSearchBarPreview() {
    ShaterAITheme {
        ShaterSearchBar(
            query = "",
            onQueryChange = {},
            modifier = Modifier.padding(16.dp)
        )
    }
}
```

### Example Feature Component: `MeetingCard.kt`
*Location: `me.dwidar.shaterai.feature.meetings.presentation.ui.components.MeetingCard.kt`*
```kotlin
package me.dwidar.shaterai.feature.meetings.presentation.ui.components

import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Card
import androidx.compose.material3.CardDefaults
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import me.dwidar.shaterai.feature.meetings.domain.model.Meeting
import me.dwidar.shaterai.ui.theme.ShaterAITheme

@Composable
fun MeetingCard(
    meeting: Meeting,
    onClick: () -> Unit,
    modifier: Modifier = Modifier
) {
    Card(
        modifier = modifier
            .fillMaxWidth()
            .clickable(onClick = onClick),
        shape = RoundedCornerShape(16.dp),
        colors = CardDefaults.cardColors(containerColor = Color.White),
        elevation = CardDefaults.cardElevation(defaultElevation = 2.dp)
    ) {
        Column(modifier = Modifier.padding(16.dp)) {
            Text(text = meeting.title)
            Text(text = "${meeting.formattedDate} • ${meeting.durationText}")
            Text(text = meeting.summaryPreview)
        }
    }
}

@Preview(showBackground = true)
@Composable
private fun MeetingCardPreview() {
    ShaterAITheme {
        MeetingCard(
            meeting = Meeting(
                id = "1",
                title = "Q3 Product Roadmap",
                formattedDate = "Sep 15, 10:00 AM",
                durationText = "45 min",
                summaryPreview = "Decided to align Q3 delivery dates with marketing milestone."
            ),
            onClick = {},
            modifier = Modifier.padding(16.dp)
        )
    }
}
```
