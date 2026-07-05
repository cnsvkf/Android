https://velog.io/@hogu59/jetpack-compose-beginner-10


- Modifier.animateItem()은 “리스트 자체”가 아니라 LazyColumn/LazyRow 안의 각 item Composable에 붙이는 Modifier


#### Card 안에 직접 Modifier.animateItem()을 안 붙히는 이유 

:리스트 전용 컴포저블이 돼서

## 기본 구조

```kotlin
@Composable
fun ProjectList(
    projects: List<ProjectUiModel>,
    expandedProjectIds: Set<Long>,
    onProjectExpandClick: (Long) -> Unit
) {
    LazyColumn(
        verticalArrangement = Arrangement.spacedBy(12.dp)
    ) {
        items(
            items = projects,
            key = { project -> project.id }
        ) { project ->
            ImportantProjectCard(
                project = project,
                isExpanded = project.id in expandedProjectIds,
                onExpandClick = {
                    onProjectExpandClick(project.id)
                },
                modifier = Modifier.animateItem()
            )
        }
    }
}
```

-> ImportantProjectCard가 modifier를 받을 수 있게 바꿔야 한다.

```kotlin
@Composable
fun ImportantProjectCard(
    project: ProjectUiModel,
    isExpanded: Boolean,
    onExpandClick: () -> Unit,
    modifier: Modifier = Modifier
) {
    val transition = updateTransition(
        targetState = project.isImportant,
        label = "ImportantProjectTransition"
    )

    val cardElevation by transition.animateDp(
        transitionSpec = { tween(durationMillis = 300) },
        label = "CardElevationAnimation"
    ) { isImportant ->
        if (isImportant) 8.dp else 2.dp
    }

    Card(
        modifier = modifier
            .fillMaxWidth()
            .animateContentSize()
            .clickable(onClick = onExpandClick),
        elevation = CardDefaults.cardElevation(defaultElevation = cardElevation)
    ) {
        Column(
            modifier = Modifier.padding(16.dp)
        ) {
            Text(text = project.title)

            if (isExpanded) {
                Spacer(modifier = Modifier.height(8.dp))
                Text(text = project.description)
            }
        }
    }
}
```