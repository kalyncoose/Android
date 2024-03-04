---
tags:
  - android
  - compose
  - material3
  - done
---
Video: https://www.youtube.com/watch?v=c8XP_Ee7iqY
- General rules:
	- Only used for navigating between prominent screens
	- Use if you have three to five destinations
		- Do not use if you only have two
	- Not to be used for showing contextual actions within screens
		- Use top app bar or bottom app bar for those
	- Can show badges, with or without labels, to indicate new content
	- Cannot be used simultaneously with a bottom app bar
	- Best used for small devices like phones and smaller tablets
	- For larger tablets and bigger devices, use a navigation rail instead
- Setup a data class to help with defining the navigation bar content:
```kotlin
data class BottomNavigationItem(
    val title: String,
    val selectedIcon: ImageVector,
    val unselectedIcon: ImageVector,
    val hasNews: Boolean,
    val badgeCount: Int? = null
)
```
- Create list of navigation items:
```kotlin
val items = listOf(
	BottomNavigationItem(
		title = "Home",
		selectedIcon = Icons.Filled.Home,
		unselectedIcon = Icons.Outlined.Home,
		hasNews = false,
	),
	BottomNavigationItem(
		title = "Chat",
		selectedIcon = Icons.Filled.Email,
		unselectedIcon = Icons.Outlined.Email,
		hasNews = false,
		badgeCount = 45
	),
	BottomNavigationItem(
		title = "Settings",
		selectedIcon = Icons.Filled.Settings,
		unselectedIcon = Icons.Outlined.Settings,
		hasNews = true,
	)
)
```
- Create state for the selected item:
```kotlin
var selectedItemIndex by rememberSaveable {
	mutableIntStateOf(0)
}
```
- Optional: Set `alwaysShowLabel = false` if you want only the selected item to display it's label
- Example:
```kotlin
Surface(
	modifier = Modifier.fillMaxSize(),
	color = MaterialTheme.colorScheme.background
) {
	Scaffold(
		modifier = Modifier.fillMaxSize(),
		bottomBar = {
			NavigationBar {
				items.forEachIndexed { index, item ->
					NavigationBarItem(
						selected = selectedItemIndex == index,
						onClick = {
							// Set the item to selected
							selectedItemIndex = index
							// if you had Compose navigation setup:
							// navContoller.navigate(item.title)... 
						},
						label = {
							Text(text = item.title)
						},
						icon = {
							BadgedBox(
								badge = {
									// Only show badge if there is a count
									if(item.badgeCount != null) {
										// Show badge with a number
										Badge {
											Text(text = item.badgeCount.toString())
										}
									} else if (item.hasNews) {
										// Show badge as a dot indicator
										Badge()
									}
								}
							) {
								Icon(
									// Show the selected icon if index matches
									imageVector = if (index == selectedItemIndex) {
										item.selectedIcon
									} else item.unselectedIcon,
									contentDescription = item.title
								)
							}
						})
					}
				}
			}
	) {
		// Screen content goes here...
	}
}
```
