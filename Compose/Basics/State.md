---
tags:
  - android
  - done
  - compose
  - compose_basics
---
Video: https://www.youtube.com/watch?v=s3m1PSd7VWc&list=PLQkwcJG4YTCSpJ2NLhDTHhi6XBNfk9WiC&index=6
- State describes how our given UI looks at a moment
- Example: An increment counter button where a counter variable is updated and the updated number is shown in the UI
- Box composable that changes color when clicked:
  ```kotlin
@Composable  
fun ColorBox(modifier: Modifier = Modifier) {  
	val color = remember {  
		mutableStateOf(Color.Yellow)  
	}  
	  
	Box(modifier = modifier  
		.background(color.value)  
		.clickable {  
			color.value = Color(  
			Random.nextFloat(),  
			Random.nextFloat(),  
			Random.nextFloat(),  
			1f  
			)  
		}  
	)  
}
	```
- `mutuableStateOf()` tracks a state value, can be given a default like `Color.Yellow`
- You can access the value of the state variable with `color.value`
- You can set the value of the variable with `color.value = something`
- `remember { }` is a Compose lambda function that retains the composable's state value when re-rendering