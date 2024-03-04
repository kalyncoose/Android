---
tags:
  - android
  - compose
  - material3
  - done
---
Video: https://www.youtube.com/watch?v=BezaAlslYDQ
- Navigation rail is useful for larger devices like tablets
	- Where bottom navigation bar would be too wide for a tablet
	- Can have a navigation icon (e.g. hamburger menu)
	- Can have a floating action button
	- Can have navigation items
- Add this dependency to calculate window size:
	- `implementation("androidx.compose.material3:material3-window-size-class:1.2.0")`
- Define a data class for navigation items:
```kotlin
data class NavigationItem(
    val title: String,
    val selectedIcon: ImageVector,
    val unselectedIcon: ImageVector,
    val hasNews: boolean,
    val badgeCount: Int? = null
)
```
- Create a list of navigation items:
```kotlin
val items = listOf(
	NavigationItem(
		title = "Home",
		selectedIcon = Icons.Filled.Home,
		unselectedIcon = Icons.Outlined.Home,
		hasNews = false,
	),
	NavigationItem(
		title = "Chat",
		selectedIcon = Icons.Filled.Email,
		unselectedIcon = Icons.Outlined.Email,
		hasNews = false,
		badgeCount = 45
	),
	NavigationItem(
		title = "Settings",
		selectedIcon = Icons.Filled.Settings,
		unselectedIcon = Icons.Outlined.Settings,
		hasNews = true,
	)
)
```
- Calculate current window size and setup navigation state:
```kotlin
val windowClass = calculateWindowSizeClass(this)
val showNavigationRail = windowClass.widthSizeClass != WindowWidthSizeClass.Compact
var selectedItemIndex by rememberSaveable {
	mutableIntStateOf(0)
}
```
- Create a navigation icon component to manage which icon is displayed and badges:
```kotlin
@Composable
fun NavigationIcon(
    item: NavigationItem,
    selected: Boolean
) {
    BadgedBox(
        badge = {
            if(item.badgeCount != null) {
                Badge {
                    Text(text = item.badgeCount.toString())
                }
            } else if(item.hasNews) {
                Badge()
            }
        }
    ) {
        Icon(
            imageVector = if(selected) item.selectedIcon else item.unselectedIcon,
            contentDescription = item.title
        )
    }
}
```
- Create a navigation side bar component to implement the navigation rail:
```kotlin
@Composable
fun NavigationSideBar(
    items: List<NavigationItem>,
    selectedItemIndex: Int,
    onNavigate: (Int) -> Unit
) {
    NavigationRail(
        header = {
            IconButton(onClick = { /*TODO*/ }) {
                Icon(
                    imageVector = Icons.Default.Menu,
                    contentDescription = "Menu"
                )
            }
            FloatingActionButton(
                onClick = { /*TODO*/ },
                elevation = FloatingActionButtonDefaults.bottomAppBarFabElevation()
            ) {
                Icon(
                    imageVector = Icons.Default.Add,
                    contentDescription = "Add"
                )
            }
        },
        modifier = Modifier
            .background(MaterialTheme.colorScheme.inverseOnSurface)
            .offset(x = (-1).dp)
    ) {
        items.forEachIndexed { index, item ->
            Column(
                modifier = Modifier.fillMaxHeight(),
                verticalArrangement = Arrangement.spacedBy(12.dp, Alignment.Bottom)
            ) {
                NavigationRailItem(
                    selected = selectedItemIndex == index,
                    onClick = {
                        onNavigate(index)
                    },
                    icon = {
                        NavigationIcon(
                            item = item,
                            selected = selectedItemIndex == index
                        )
                    },
                    label = {
                        Text(text = item.title)
                    }
                )
            }
        }
    }
}
```
- Now display the navigation side bar along with some screen content:
```kotlin
Surface(
	modifier = Modifier.fillMaxSize(),
	color = MaterialTheme.colorScheme.background
) {
	Scaffold(
		bottomBar = {
			if(!showNavigationRail) {
				// Show bottom navigation bar on compact devices here...
			}
		},
		modifier = Modifier.fillMaxSize()
	) {
		LazyColumn(
			modifier = Modifier
				.fillMaxSize()
				.padding(it)
				.padding(
					start = if (!showNavigationRail) 80.dp else 0.dp
				)
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

// Put navigation rail outside of the surface
if(!showNavigationRail) { 
	// Using ! to show on phone emulator, too lazy to setup tablet emulator
	NavigationSideBar(
		items = items,
		selectedItemIndex = selectedItemIndex,
		onNavigate = {
			selectedItemIndex = it
		}
	)
}
```
