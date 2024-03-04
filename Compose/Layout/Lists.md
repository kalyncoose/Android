---
tags:
  - android
  - done
  - compose
  - layout
---
Video: https://www.youtube.com/watch?v=1Thp0bB5Ev0&list=PLQkwcJG4YTCSpJ2NLhDTHhi6XBNfk9WiC&index=8
- When you have a set amount and small of items to display, simply use a `Column` like normal
- However, when displaying large or unknown amount of items it is best to use a `LazyColumn
	- `LazyColumn` and `LazyRow` are similar to `RecyclerView` from the XML days
	- This is much simpler than a `RecyclerView` which took many steps to create
- To make a `Column` scrollable, you need to add state and a modifier:
	- State: `val scrollState = rememberScrollState()`
	- Modifier: `modifier = Modifier.verticalScroll(scrollState)`
- 50 items is already too much for a regular `Column` that is scrollable
	- `Column` renders all items on app startup (bad performance)
	- `LazyColumn` only renders items that come into scroll position (good performance)
- `LazyColumn` works differently than a `Column` with scroll state
	- Is scrollable by default
	- Comes with looping functionality
- Example: `LazyColumn` with `items` block
  ```kotlin
  LazyColumn {  
	items(5000) {  
		// composables...
    }
  }
	```
- Can use `itemsIndexed()` instead of `items()`:
  ```kotlin
  LazyColumn {  
	itemsIndexed(  
		listOf("This", "is", "Jetpack", "Compose")  
	) { index, string ->  
		// composables...  
    }
  }
	```
