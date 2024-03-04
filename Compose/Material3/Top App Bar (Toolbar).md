---
tags:
  - android
  - done
  - compose
  - material3
---
Video: https://www.youtube.com/watch?v=EqCvUETekjk
- Two main reasons to use a top app bar:
	- Indicate the name of screen to the user
	- Provide actions for the user on that screen
		- Navigation (back button), heart, edit, etc.
- New with Material3:
	- Scroll effect for the top app bar to change background color slightly when user begins scrolling
- Top App Bar comes in three distinct sizes and one style variation:
	- `TopAppBar` - The default, small size. Everything takes up one row.
	- `MediumTopAppBar` - The medium size, takes up two rows. Useful for accommodating more text in the app bar.
	- `LargeTopAppBar` - The large size, takes up multiple rows. Makes the top bar more prominent for the design.
	- `CenterAlignedTopAppBar` - Same as the default top app bar, but center aligned
- `title` - Set the title for the app bar
- `navigationIcon` - Optionally set an icon button for navigation on the right before the title
	- Examples: 
		- Hamburger menu to open a drawer
		- Back arrow to navigate back to a previous screen
- `actions` - A set of optional icon buttons
	- You are allowed up to three, if you have more then a 3-dots context menu should be utilized to provide more actions
- `scrollBehavior` - Set the behavior for what happens when the user scrolls
	- Set behavior with `val scrollBehavior = TopAppBarDefaults.<option>`
	- Behavior options:
		- `exitUntilCollapsedScrollBehavior` - Tool bar gets hidden unless at the very top
		- `pinnedScrollBehavior` - Tool bar is always showing but will change colors when scrolling
		- `enterAlwaysScrollBehavior` - Tool bar gets hidden when scrolling up, but always gets shown when scrolling down
	- Add behavior to the `TopAppBar` with `scrollBehavior = scrollBehavior`
	- Connect the scrolling as a modifier on the `Scaffold` with `modifier.nestedScroll(scrollBehavior.nestedScrollConnection),`
- Example:
```kotlin
Surface(
	modifier = Modifier.fillMaxSize(),
	color = MaterialTheme.colorScheme.background
) {
	val scrollBehavior = TopAppBarDefaults.pinnedScrollBehavior()
	Scaffold(
		modifier = Modifier
			.fillMaxSize()
			.nestedScroll(scrollBehavior.nestedScrollConnection),
		topBar = {
			TopAppBar(
				title = {
					Text(text = "My notes")
				},
				navigationIcon = {
					IconButton(onClick = { /*TODO*/ }) {
						Icon(
							imageVector = Icons.Filled.ArrowBack,
							contentDescription = "Go back"
						)
					}
				},
				actions = {
					IconButton(onClick = { /*TODO*/ }) {
						Icon(
							imageVector = Icons.Default.FavoriteBorder,
							contentDescription = "Mark as favorite"
						)
					}
					IconButton(onClick = { /*TODO*/ }) {
						Icon(
							imageVector = Icons.Default.Edit,
							contentDescription = "Edit notes"
						)
					}
				},
				scrollBehavior = scrollBehavior
			)
		}
	) { values ->
		LazyColumn(
			modifier = Modifier
				.fillMaxSize()
				.padding(values)
		) {
			items(100) {
				Text(
					text = "Item$it",
					modifier = Modifier.padding(16.dp)
				)
			}
		}
	}
}
```
  