---
tags:
  - android
  - done
  - kotlin
  - coroutines
---
Video: https://www.youtube.com/watch?v=71NrkkRNXG4&list=PLQkwcJG4YTCQcFEPuYGuv54nYai_lwil_&index=4
- In general, coroutines are always started in a context
- Original example:
```kotlin
GlobalScope.launch {
	// ...
}
```
- Now with a custom dispatcher:
```kotlin
GlobalScope.launch(Dispatchers.Main) {
	// ...
}
```
- Difference between dispatchers:
	- `Main` - Primary thread of the app, useful for updating the UI since you can only change the UI from the `Main` thread
	- `IO` - Useful for all kinds of data operations like making network calls, writing to databases, reading/writing to files
	- `Default` - Choose if you're planning on running complex and long running operations that would block the `Main` thread
		- Example: sort a list of 10,000 elements
	- `Unconfined` - Not confined to a specific thread, can stay in a thread that a suspend function resumed
- Start a coroutine on your own thread:
```kotlin
GlobalScope.launch(newSingleThreadContext("MyThread")) {
	// ...
}
```
- Text:
```kotlin
// Start network call in IO thread to not delay Main thread
GlobalScope.launch(Dispatchers.IO) {
	val result = doNetworkCall()
	// Switch context when you have the result
	withContext(Dispatchers.Main) {
		// Set result to show in some text in UI
		someText = result
	}
}
```
- Text