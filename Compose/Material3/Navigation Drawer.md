---
tags:
  - android
  - compose
  - material3
  - done
---
Video: https://www.youtube.com/watch?v=aYSarwALlpI
- Define a data class for navigation items:
```kotlin
data class NavigationItem(
    val title: String,
    val selectedIcon: ImageVector,
    val unselectedIcon: ImageVector,
    val badgeCount: Int? = null
)
```
- Note that the navigation drawer goes around a scaffold
- There are three types of navigation drawers:
	- `ModalNavigationDrawer` - Standard slide out from left drawer with modality, drawer appears above dimmed content
	- `PermanentNavigationDrawer` - Fixed drawer for usage on larger devices like tablets, takes up its own space
	- `DismissibleNavigationDrawer` - Like the fixed drawer but can be dismissed, pushes content when expanded and widens content when collapsed
- `ModalNavigationDrawer` example:
```kotlin
Surface(
	modifier = Modifier.fillMaxSize(),
	color = MaterialTheme.colorScheme.background
) {
	val drawerState = rememberDrawerState(initialValue = DrawerValue.Closed)
	val scope = rememberCoroutineScope()
	var selectedItemIndex by rememberSaveable {
		mutableIntStateOf(0)
	}
	ModalNavigationDrawer(
		drawerContent = {
			ModalDrawerSheet {
				Spacer(modifier = Modifier.height(16.dp))
				items.forEachIndexed { index, item ->
					NavigationDrawerItem(
						label = { 
							Text(text = item.title)
						},
						selected = index == selectedItemIndex,
						onClick = {
							selectedItemIndex = index
							scope.launch {
								drawerState.close()
							}
						},
						icon = {
							Icon(
								imageVector = if (index == selectedItemIndex) {
									item.selectedIcon
								} else item.unselectedIcon,
								contentDescription = item.title
							)
						},
						badge = {
							item.badgeCount?.let {
								Text(text = item.badgeCount.toString())
							}
						},
						modifier = Modifier
							.padding(NavigationDrawerItemDefaults.ItemPadding)
					)
				}
			}
		},
		drawerState = drawerState
	) {
		Scaffold(
			topBar = {
				TopAppBar(
					title = { 
						Text(text = "Todo App")
					},
					navigationIcon = {
						IconButton(onClick = {
							scope.launch {
								drawerState.open()
							}
						}) {
							Icon(
								imageVector = Icons.Default.Menu,
								contentDescription = "Menu"
							)
						}
					}
				)
			}
		) {
			// Screen content goes here...
		}
	}
}
```
