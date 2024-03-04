---
tags:
  - android
  - done
  - kotlin
  - coroutines
---
Video: https://www.youtube.com/watch?v=kvfpuzSwVZ8&list=PLQkwcJG4YTCQcFEPuYGuv54nYai_lwil_&index=2
- May need dependencies in your `build.gradle` file:
```kotlin
implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.3.5'
implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.5'
```
- Every coroutine needs a scope to start in
	- Example:
	  ```kotlin
	  // GlobalScope creates a new thread separate from main thread
	  GlobalScope.launch {
		  Log.d("Coroutine is ${Thread.currentThread().name}")
	  }
		```
- Note that you would not want to use `GlobalScope` in many cases, this is just for an example
- Coroutines have their own sleep function called `delay()`
	- Example: `delay(3000L)` for 3,000 milliseconds which is equivalent to 3 full seconds
- **IMPORTANT**: `delay()` only blocks the current coroutine and not the whole thread like Java `sleep()` does