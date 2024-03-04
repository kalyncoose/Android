---
tags:
  - android
  - done
  - compose
  - media
---
Video: https://www.youtube.com/watch?v=KPVoQjwmWX4&list=PLQkwcJG4YTCSpJ2NLhDTHhi6XBNfk9WiC&index=4
- Create composables like:
  ```kotlin
  @Composable
  fun ImageCard(
	  // args...
  ) {
  // body...
  }
	```
- Composable functions follow `TitleCase` where regular Kotlin functions follow `camelCase`
- If you want content to overlay on each other then you want to stack it (not using columns or rows)
	- Whatever is written first is the bottom layer of the stack
	- Example:
		- Card -> Image -> Gradient -> Text
- A `contentAlignment` arg on the `Box` composable is different than Columns and Rows, since it has no main or cross axis it is just a way to align the content of the `Box` in one arg
- `.sp` unit is preferred for `Text` sizing on Android (scales with user's font size setting where as `.dp` won't)
- Finished `ImageCard` composable:
  ```kotlin
@Composable  
fun ImageCard(  
	painter: Painter,  
	contentDescription: String,  
	title: String,  
	modifier: Modifier = Modifier  
) {  
	Card(  
		modifier = modifier.fillMaxWidth(),  
		shape = RoundedCornerShape(15.dp),  
	) {  
		Box(modifier = Modifier.height(200.dp)) {  
			Image(  
				painter = painter,  
				contentDescription = contentDescription,  
				contentScale = ContentScale.Crop  
			)  
			Box(modifier = Modifier  
				.fillMaxSize()  
				.background(  
					Brush.verticalGradient(  
						colors = listOf(  
						Color.Transparent,  
						Color.Black,  
						),  
					startY = 300f,  
					)  
				)  
			)  
			Box(  
				modifier = Modifier  
				.fillMaxSize()  
				.padding(12.dp),  
				contentAlignment = Alignment.BottomStart  
			) {  
			Text(title, style = TextStyle(color = Color.White, fontSize = 16.sp))  
			}  
		}  
	}  
}
	```
- Using the composable:
```kotlin
// Setup the variables for the ImageCard
val painter = painterResource(id = R.drawable.windy_pikes_peak)  
val description = "Picture of a windy mountain"  
val title = "The wind at Pikes Peak is fast"  

// Fit the card
Box(modifier = Modifier  
	.fillMaxWidth(0.5f)  
	.padding(16.dp)  
) {  
	// Create the card
	ImageCard(  
		painter = painter,  
		contentDescription = description,  
		title = title  
	)  
}
	```
