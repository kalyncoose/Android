---
tags:
  - android
  - done
  - kotlin
  - coroutines
---
Video: https://www.youtube.com/watch?v=yc_WfBp-PdE&list=PLQkwcJG4YTCQcFEPuYGuv54nYai_lwil_&index=3
- Coroutines have a suspend functionality
- Suspend functions can only be executed inside a coroutine or other suspend functions
- Syntax example: `suspend fun delay(timeMillis: Long)`
- Function example:
```kotlin
suspend fun doNetworkCall(): String {
	delay(3000L)
	return "This is the response"
}
```
- Usage example:
```kotlin
GlobalScope.launch {
	val networkResult = doNetworkCall() // delays 3 sec
	val networkResultAgain = doNetworkCall() // coroutine pauses for first network call 
}
```
