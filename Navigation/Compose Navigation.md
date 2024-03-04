---
tags:
  - android
  - navigation
  - done
---
Video: https://www.youtube.com/watch?v=4gUeyNkGE3g
- Navigation can be setup to work directly with Compose
- Install this dependency:
```kotlin
implementation("androidx.navigation:navigation-compose:2.7.7")
```
- Key concepts:
	- `Host` - UI element that contains the current navigation destination
	- `Graph` - Data structure that defines all navigation destinations and how they connect
	- `Controller` - Manages navigation between destinations, offers methods, handles deep links, manages back stack, etc.
- Define a `Screen` class to help manage screens:
```kotlin
sealed class Screen(
    val route: String
) {
    object MainScreen : Screen("main_screen")
    object DetailScreen : Screen("detail_screen")

    fun withArgs(vararg args: String): String {
        return buildString {
            append(route)
            args.forEach { arg ->
                append("/$arg")
            }
        }
    }
}
```
- Each `Screen` requires a `route` string
- In this example we have two screens: `MainScreen` and `DetailScreen`
- The `withArgs` function provides a way to easily append args to the route
- Setup a `Navigation` composable with a `navController`:
```kotlin
@Composable
fun Navigation() {
	val navController = rememberNavController()
}
```
- Add a `NavHost` to define the `startDestination` and `NavGraph`:
```kotlin
NavHost(navController = navController, startDestination = Screen.MainScreen.route) {
	// NavGraphBuilder in here
}
```
- Add `composable()` screens to the `NavGraphBuilder` inside the `NavHost` lambda block:
```kotlin
// Create a new route for MainScreen
composable(route = Screen.MainScreen.route) {
	// Point route to the MainScreen composable
	MainScreen(navController = navController)
}

// Create a new route for DetailScreen
composable(
	route = Screen.DetailScreen.route + "/{name}", // + "?name={name}" for optional arg
	// Create list of arguments the route requires
	arguments = listOf(
		// Define an arg "name"
		navArgument("name") {
			type = NavType.StringType
			defaultValue = "Philipp"
			nullable = true
		}
	)
) { entry -> // What the route is given when NavHost goes to the route
	DetailScreen(name = entry.arguments?.getString("name"))
}
```
- Create a `MainScreen` composable:
```kotlin
@Composable
fun MainScreen(navController: NavController) {
    var text by remember {
        mutableStateOf("")
    }
    Column(
        verticalArrangement = Arrangement.Center,
        modifier = Modifier
            .fillMaxWidth()
            .padding(50.dp)
    ) {
        TextField(
            value = text,
            onValueChange = {
                text = it
            },
            modifier = Modifier.fillMaxWidth()
        )
        Spacer(modifier = Modifier.height(8.dp))
        Button(
            onClick = {
	            // Navigate to DetailScreen with the text value input
                navController.navigate(Screen.DetailScreen.withArgs(text))
            },
            modifier = Modifier.align(Alignment.End)
        ) {
            Text(text = "To DetailScreen")
        }
    }
}
```
- Create a `DetailScreen` composable:
```kotlin
@Composable
fun DetailScreen(name: String?) {
    Box(
        modifier = Modifier.fillMaxSize(),
    )
	// Display the text that was given to the route
    Text(text = "Hello, $name")
}
```
- See [[Nested Navigation Graphs]] for a more advanced example of setting up navigation routes
