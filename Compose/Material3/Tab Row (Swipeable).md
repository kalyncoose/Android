---
tags:
  - android
  - compose
  - material3
  - done
---
Video: https://www.youtube.com/watch?v=9r4st6dmyNE
- General rules:
	- You can have tabs with just text or with text and icons
	- By default the tab row does not bring the page swiping functionality
- Define a data class to help create tab items:
```kotlin
data class TabItem(
    val title: String,
    val selectedIcon: ImageVector,
    val unselectedIcon: ImageVector,
)
```
- Create a list of tab items:
```kotlin
val tabItems = listOf(
	TabItem(
		title = "Home",
		selectedIcon = Icons.Filled.Home,
		unselectedIcon = Icons.Outlined.Home,
	),
	TabItem(
		title = "Browse",
		selectedIcon = Icons.Filled.ShoppingCart,
		unselectedIcon = Icons.Outlined.ShoppingCart,
	),
	TabItem(
		title = "Account",
		selectedIcon = Icons.Filled.AccountCircle,
		unselectedIcon = Icons.Outlined.AccountCircle,
	),
)
```
- Define state for which tab item is currently selected:
```kotlin
var selectedTabIndex by remember {
	mutableIntStateOf(0)
}
```
- Tab Row example (basic, non-swipeable):
```kotlin
Column(
	modifier = Modifier.fillMaxSize()
) {
	TabRow(selectedTabIndex = selectedTabIndex) {
		tabItems.forEachIndexed { index, item ->
			Tab(
				selected = index == selectedTabIndex,
				onClick = {
					selectedTabIndex = index
				},
				text = {
					Text(text = item.title)
				},
				icon = {
					Icon(
						imageVector = if (index == selectedTabIndex) {
							 item.selectedIcon
						 } else item.unselectedIcon,
						contentDescription = item.title
					)
				}
			)
		}
	}
}
```
- You can also implement a `ScrollableTabRow` if there are many tabs
- Now to implement the swipeable, you need to use the `HorizontalPager` composable
- Setup the pager state:
```kotlin
val pagerState = rememberPagerState {
	// Stores page count
	tabItems.size
}
```
- Create the horizontal pager:
```kotlin
HorizontalPager(
	state = pagerState,
	modifier = Modifier.fillMaxWidth(1f)
) { index ->
	Box(
		modifier = Modifier.fillMaxSize(),
		contentAlignment = Alignment.Center
	) {
		Text(text = tabItems[index].title)
	}
}
```
- Now we need to connect the pager state to the tab row state, using two launched effects:
```kotlin
// Listen for selected tab change
LaunchedEffect(selectedTabIndex) {
	// Tell pager to scroll to corresponding page
	pagerState.animateScrollToPage(selectedTabIndex)
}

// Listen for pager being swiped
LaunchedEffect(pagerState.currentPage, pagerState.isScrollInProgress) {
	// Don't run logic if pager is currently scrolling
	if (!pagerState.isScrollInProgress) {
		// Tell tab row to select corresponding tab
		selectedTabIndex = pagerState.currentPage
	}
}
```
