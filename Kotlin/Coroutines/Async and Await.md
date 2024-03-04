---
tags:
  - android
  - done
  - kotlin
  - coroutines
---
Video: https://www.youtube.com/watch?v=t-3TOke8tq8&list=PLQkwcJG4YTCQcFEPuYGuv54nYai_lwil_&index=7
- If you have several suspend functions and execute them both in a coroutine then they are ran sequentially by default
- However, if you want to do two network calls at the same time for example then you can use `async` blocks and `await()`
	- This is a better option than just starting two different coroutine jobs with one call each
- Example usage:
```kotlin
GlobalScope.launch(Dispatchers.IO) {
	val time = measureTimeMillis {
		val answer1 = async { networkCall1() }
		val answer2 = async { networkCall2() }
		Log.d("Answer1 is ${answer1.await()}")
		Log.d("Answer2 is ${answer2.await()}")
	}
	Log.d("Requests took $time ms")
}
suspend fun networkCall1(): String {
	delay(3000L)
	return "Answer 1"
}
suspend fun networkCall2(): String {
	delay(3000L)
	return "Answer 2"
}
```
- The result of a `async { ... }` block is always a `Deferred<T>` type
	- To use the result later, use `variable.await()`