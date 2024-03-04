---
tags:
  - android
  - done
  - kotlin
  - coroutines
---
Video: https://www.youtube.com/watch?v=55W60o9uzVc&list=PLQkwcJG4YTCQcFEPuYGuv54nYai_lwil_&index=6
- When you create a new coroutine it actually creates a job
```kotlin
// Store the created job in a variable
val job = GlobalScope.launch(Dispatchers.Default) {
	repeat(5) {
		Log.d("Coroutine is still working...")
		delay(1000L)
	}
}
// Wait for a job to finish with join()
runBlocking {
	job.join()
	Log.d("Main thread is continuing...")
}
```
- `.join()` is a suspend function so it must be called within a coroutine as well
- Use `job.cancel()` to cancel an existing/running job
- Canceling a coroutine is not usually this easy, because it needs to be a cooperative operation where the coroutine knows it is being canceled and runs any final logic on cancelation
	- Example: Canceling a recursive Fibonacci function
	  ```kotlin
	  val job = GlobalScope.launch(Dispatchers.Default) {
		  for(i in 30..40) {
			  if(isActive) { // Check if job is still active
				  Log.d("Result for i = $i: ${fib(i)}") // fib() calculates fib number
			  }
		  }
	  }
	  
	  runBlocking {
		  delay(2000L)
		  job.cancel()
		  Log.d("Job canceled!")
	  }
		```
- In practice, the reason you want to cancel a coroutine is usually because of a timeout
- For example, you have a network call that takes too much time and you want to cancel it
- Coroutines come with a useful suspend function called `withTimeout() {}`:
```kotlin
// GlobalScope.launch...
withTimeout(3000L) { // automatically cancels job when time is reached if still running
	doNetworkCall()
}
```
