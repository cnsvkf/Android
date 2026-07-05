| API               | 특징                                        |
| ----------------- | ----------------------------------------- |
| `Crossfade`       | fade in/out 중심의 단순 전환                     |
| `AnimatedContent` | slide, fade, size transform 등 더 복잡한 전환 가능 |


-> _예시_

```kotlin
@Composable
fun ProjectContentSection(
    uiState: ProjectUiState,
    onProjectExpandClick: (Long) -> Unit
) {
    Crossfade(
        targetState = uiState.selectedTab,
        animationSpec = tween(durationMillis = 300),
        label = "ProjectTabCrossfade"
    ) { selectedTab ->
        when (selectedTab) {
            ProjectTab.RECOMMENDED -> {
                ProjectList(
                    projects = uiState.projects,
                    expandedProjectIds = uiState.expandedProjectIds,
                    onProjectExpandClick = onProjectExpandClick
                )
            }

            ProjectTab.PARTICIPATING -> {
                ProjectList(
                    projects = uiState.projects.filter { project -> project.isImportant },
                    expandedProjectIds = uiState.expandedProjectIds,
                    onProjectExpandClick = onProjectExpandClick
                )
            }
        }
    }
}
```