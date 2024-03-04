---
tags:
  - android
  - done
  - compose
  - layout
---
Video: https://www.youtube.com/watch?v=rHKeRWK3zL4&list=PLQkwcJG4YTCSpJ2NLhDTHhi6XBNfk9WiC&index=2
- By default, Compose places composables one over the other
	- Also compose only renders things as large as they need to be
	- You need to use `Column` and `Row` to make composables sit near each other
```kotlin
Column {
	Text("Hellow")
	Text("World")
}
Row {
	Text("Hellow")
	Text("World")
}
```
- Difference between "Alignment" and "Arrangement":
	- Each column and each row have a main axis and a cross axis
	- Main axis is the direction in which new items are stacked
- Column
	- `verticalArrangement` - how to arrange items in the main axis
	- `horizontalAlignment` - how to arrange items in the cross axis
- Row
	- `horizontalArrangement` - how to arrange items in the main axis
	- `verticalAlignment` - how to arrange items in the cross axis
- Modifiers
  ```kotlin
  modifier = Modifier  
	.fillMaxSize(0.8f)  
	.background(Color.Green),  
	horizontalArrangement = Arrangement.SpaceAround,  
	verticalAlignment = Alignment.CenterVertically
	```
- `.fillMaxSize()` defaults to `1f` meaning fill 100%, or you can set a custom fraction
	- Optionally use `.fillMaxHeight()` or `.fillMaxWidth()`
- Can use `.width` and `.height` to set specific units like `200.dp`
- `.background(Color.Green)` to see area taken up by composable
- Can use `.weight()` modifier in Columns/Rows so that composables take up a proportional weight