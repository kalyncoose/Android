---
tags:
  - android
  - done
  - compose
  - material3
---
Video: https://www.youtube.com/watch?v=Dlav7VIAQ3E
- General rules:
	- Use only on tablets and phones, not on larger sizes
	- Only use a bottom app bar if you have at least two actions to show
	- Bottom app bar should not be used for navigation, that is a separate navigation bar
	- Can display a floating action button inside the bottom app bar for a prominent action
- Example:
```kotlin
Surface(
	modifier = Modifier.fillMaxSize(),
	color = MaterialTheme.colorScheme.background
) {
	Scaffold(
		modifier = Modifier.fillMaxSize(),
		bottomBar = {
			BottomAppBar(
				actions = {
					IconButton(onClick = { /*TODO*/ }) {
						Icon(
							imageVector = Icons.Default.Share,
							contentDescription = "Share contact"
						)
					}
					IconButton(onClick = { /*TODO*/ }) {
						Icon(
							imageVector = Icons.Default.FavoriteBorder,
							contentDescription = "Mark as favorite"
						)
					}
					IconButton(onClick = { /*TODO*/ }) {
						Icon(
							imageVector = Icons.Default.Email,
							contentDescription = "Email contact"
						)
					}
				},
				floatingActionButton = {
					FloatingActionButton(onClick = { /*TODO*/ }) {
						Icon(
							imageVector = Icons.Default.Phone,
							contentDescription = "Call contact"
						)
					}
				}
			)
		}
	) {
		// Add a lazy column of contact list items, etc.
	}
}
```
  