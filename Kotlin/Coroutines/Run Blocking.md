---
tags:
  - android
  - done
  - kotlin
  - coroutines
---
Video: https://www.youtube.com/watch?v=k9yisEEPC8g&list=PLQkwcJG4YTCQcFEPuYGuv54nYai_lwil_&index=5
- The `runBlocking` scope starts a new coroutine in the `Main` thread and will block the `Main` thread when suspended
- Useful for when you don't necessarily need the coroutine functionality but still want to call suspend functions from the `Main` thread
	- Useful in some JUnit testing scenarios
	- Can be used to play around with coroutines and see how they work
- Note: `GlobalScope.launch(Dispatchers.Main) { ... }` does not block the `Main` thread but `runBlocking { ... }` does
- Example usage:
```kotlin
runBlocking {
	delay(1000L)
}
```
- Can launch new coroutines in different contexts that don't block the `Main` thread within `runBlocking` using `launch() {}`:
```kotlin
runBlocking {
	launch(Dispatchers.IO) {
		delay(3000L)
	}
	delay(1000L)
}
```
