---
tags:
  - android
  - done
  - compose
  - compose_basics
---
Video: https://www.youtube.com/watch?v=nm_LNJWHi9A&list=PLQkwcJG4YTCSpJ2NLhDTHhi6XBNfk9WiC&index=5
- Text has a lot more modifiers available
- Can set custom colors with `Color(0xFF101010)` where`0xFF` is the prefix
- When importing font files (e.g. `.ttf` from Google Fonts), make sure to rename them to **all lowercase** and replace **dashes** with **underscores** to comply formatting
- Add a new font family:
	- Right click on `res` directory, click **New** then **Android Resource Directory** 
	- Choose directory name and resource type as `font` and click Ok
	- Move font files to the new `res/font` folder
- Create a new font family class object:
  ```kotlin
  val fontFamily = FontFamily(  
	Font(R.font.lexend_thin, FontWeight.Thin),  
	Font(R.font.lexend_light, FontWeight.Light),  
	Font(R.font.lexend_regular, FontWeight.Normal),  
	Font(R.font.lexend_medium, FontWeight.Medium),  
	Font(R.font.lexend_semibold, FontWeight.SemiBold),  
	Font(R.font.lexend_bold, FontWeight.Bold),  
	Font(R.font.lexend_extrabold, FontWeight.ExtraBold),  
)
	```
- Example usage:
  ```kotlin
  Text(  
	text = "Jetpack Compose",  
	color = Color.White,  
	fontSize = 30.sp,  
	fontFamily = fontFamily,  
	fontWeight = FontWeight.Bold,  
	fontStyle = FontStyle.Italic,  
	textAlign = TextAlign.Center,  
	textDecoration = TextDecoration.Underline,  
)
	```
- Annotated Strings all you to pass different styles to different parts of text (like of like `<span>`'s)
- Example usage:
  ```kotlin
  Text(  
	text = buildAnnotatedString {  
		withStyle(  
			style = SpanStyle(  
			color = Color.Green,  
			fontSize = 50.sp  
			)  
		) {  
		append("J")  
		}  
		append("etpack")  
		},
)
	```
