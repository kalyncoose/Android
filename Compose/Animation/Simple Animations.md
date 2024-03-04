---
tags:
  - android
  - done
  - compose
  - animation
---
Video: https://www.youtube.com/watch?v=trVmP1rw0uw&list=PLQkwcJG4YTCSpJ2NLhDTHhi6XBNfk9WiC&index=11
- You can add animations to composables
	- Simply just setup state for value like `dp` or a color that should be animated
	- Then setup an animation state with something like `val size by animateDpAsState(...)`
	- Now trigger the animation like with `onClick = { sizeState += 50.dp }`
- To define how the animation behaves you use an `AnimationSpec` like these:
	- `tween` animation:
	  ```kotlin
	  tween(
			durationMillis = 3000,
			delayMillis = 300,
			easing = LinearOutSlowInEasing
		)
	```
	- `spring` animation:
	  ```kotlin
	  spring(  
			Spring.DampingRatioHighBouncy  
		)
	```
- Create a custom `AnimationSpec` with `keyframes { ... }`:
  ```kotlin
	  keyframes {
		durationMillis = 5000
		sizeState at 0 with LinearEasing
		sizeState * 1.5f at 1000 with FastOutLinearInEasing
		sizeState * 2f at 5000
	}
	```
- Create a looping (infinite) animation with `rememberInfiniteTransition()` state and the `AnimationSpec` called `infiniteRepeatable(...)`:
  ```kotlin
    val infiniteTransition = rememberInfiniteTransition()
	val color by infiniteTransition.animateColor(
		initialValue = Color.Red,
		targetValue = Color.Green,
		animationSpec = infiniteRepeatable(
			tween(durationMillis = 2000),
			RepeatMode.Reverse
		)
	)
	```
- Full example:
```kotlin
// class...
// override fun onCreate...
	setContent {
		var sizeState by remember {
			mutableStateOf(200.dp)
		}
		val size by animateDpAsState(
			targetValue = sizeState,
			tween(durationMillis = 1000)
		)
		val infiniteTransition = rememberInfiniteTransition()
		val color by infiniteTransition.animateColor(
			initialValue = Color.Red,
			targetValue = Color.Green,
			animationSpec = infiniteRepeatable(
				tween(durationMillis = 2000),
				RepeatMode.Reverse
			)
		)
		Box(modifier = Modifier
			.size(size)
			.background(color)
		) {
			Button(onClick = {
				sizeState += 50.dp
			}) {
				Text(text = "Increase Size")
			}
		}
	}
```
