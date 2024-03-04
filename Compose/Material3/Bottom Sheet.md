---
tags:
  - android
  - compose
  - material3
  - done
---
Video: https://www.youtube.com/watch?v=VxgWUdOKgtI
- Bottom sheet can be used to display any kind of information or interactions
- API to show/hide bottom sheet is not intuitive unfortunately
	- Bottom sheet appears by default so you need an additional state variable to control whether it is shown and use conditional rendering to show/hide the bottom sheet
- Setup the state:
```kotlin
val sheetState = rememberModalBottomSheetState()
var isSheetOpen by rememberSaveable {
	mutableStateOf(false)
}
```
- Cannot use `sheetState.expand()` or `sheetState.hide()` as expected because otherwise it is shown by default
- Cannot use `sheetState.isVisible` because that is only set after the sheet state changes for checking the current visibility, not to be used for conditional rendering
- Can implement `dragHandle = ...` if you want to change the default
- `ModalBottomSheet` example:
```kotlin
Surface(
	modifier = Modifier.fillMaxSize(),
	color = MaterialTheme.colorScheme.background
) {
	val sheetState = rememberModalBottomSheetState()
	var isSheetOpen by rememberSaveable {
		mutableStateOf(false)
	}
	Box(
		modifier = Modifier.fillMaxSize(),
		contentAlignment = Alignment.Center
	) {
		Button(onClick = {
			isSheetOpen = true
		}) {
			Text(text = "Open sheet")
		}
	}
	if (isSheetOpen) {
		ModalBottomSheet(
			sheetState = sheetState,
			onDismissRequest = {
				isSheetOpen = false
			}
		) {
			// Whatever content you want in the sheet goes here...
			Image(
				painter = painterResource(id = R.drawable.windy_pikes_peak),
				contentDescription = "Image"
			)
		}
	}
}
```
- Can implement a different type of bottom sheet with `BottomSheetScaffold`
	- Used for a "peeking" bottom sheet that a user can drag upwards to show the rest
	- Does not have modal functionality anymore
		- No dimming or tap outside to dismiss sheet
