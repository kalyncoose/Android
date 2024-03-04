---
tags:
  - android
  - done
  - kotlin
  - flows
---
Video: https://www.youtube.com/watch?v=sk3svS_fzZM&list=PLQkwcJG4YTCQHCppNAQmLsj_jW38rU9sC&index=2
- A flow operator is a function that handles an emission of a flow
-
- Use `.filter {}` with a Boolean expression in the filter body to filter out emissions
	- Example: Filter out odd numbers from the `time` emission
	  ```kotlin
	  .filter { time ->
		  time % 2 == 0
	  }
	```
- Use `.map {}` to map the current emission to a new value
	- Example: Take the `time` emission and return itself squared
	  ```kotlin
	  .map { time ->
		  time * time
	  }
		```
- Note that just like chaining modifiers applies styles in the order written, chaining operators applies the operators in the order written as well
	- For example, applying a `.filter` that filters odd numbers means that the `.map` afterwards now only has even values
- Use `.onEach {}` to do whatever logic you'd like using the current emission
	- Example: Print the current emission
	  ```kotlin
	  .onEach { time ->
		  println(time)
	  }
		```
- There are different types of operators called `terminal` operators, these ones terminate the flow once invoked
- Use `.count {}` to count the emission values that meet a condition
	- Example: Count all of the `time` emissions that are even numbers
	  ```kotlin
	  .count { time ->
		  time % 2 == 0
	  }
		```
- Use `.reduce {}` to apply logic to two values, an `accumulator` which is a running total and `value` which is the current emission
	- Example: Add the `accumulator` and the `value`
	  ```kotlin
	  .reduce { accumulator, value ->  
		  accumulator + value
	  }
		```
	- If we had a flow that was emitting 5 through 0, then the `.reduce` example would return a final result of `15` because `0+1+2+3+4+5=15`
- Use `.fold {}` for the same thing as `.reduce {}` but has an `initial` value
	- Example:
	  ```kotlin
	  .fold(100) { accumulator, value ->
		  accumulator + value
	  }
		```
- You can flatten multiple flows together, like flattening a list of lists into one singular list
	- Example:
```kotlin
private fun collectFlow() {
	val flow1 = flow {
		emit(1)
		delay(500L)
		emit(2)
	}
	viewModelScope.launch {
		flow1.flatMapConcat { value ->
			flow {
				emit(value + 1)
				delay(500L)
				emit(value + 2)
			}
		}.collect { value ->
			println("The value is $value") // Prints 2, 3, 3, 4
		}
	}
}
```
- `.flatMapConcat {}` runs in order for every emission
- `.flatMapMerge {}` runs all emissions at once (not recommended, use sparingly)
- `.flatMapLatest {}` runs like `.collectLatest {}` where it can cancel the existing block if a new emission comes in while the old block is running
- A more realistic example of flattening flows: a recipe app, where you have a local database of recipes and a remote database of recipes - the full list can be merged by calling both sources and flattening the data with this technique
- You can quickly create a flow with `.asFlow()`: `val flow1 = (1..5).asFlow()`
- Here is a simple example of a restaurant using two flows:
  ```kotlin
  private fun collectFlow() {
	val flow = flow {
		delay(250L)
		emit("Appetizer")
		delay(1000L)
		emit("Main dish")
		delay(100L)
		emit("Dessert")
	}
	viewModelScope.launch {
		flow.onEach {
			println("FLOW: $it is delivered")
		}.collect {
			println("FLOW: Now eating $it")
			delay(1500L)
			println("FLOW: Finished eating $it")
		}
	}
}
	```
- Which returns the logs:
```
FLOW: Appetizer is delivered
FLOW: Now eating Appetizer
FLOW: Finished eating Appetizer
FLOW: Main dish is delivered
FLOW: Now eating Main dish
FLOW: Finished eating Main dish
FLOW: Dessert is delivered
FLOW: Now eating Dessert
FLOW: Finished eating Dessert
```
- All of the suspending calls in the `.collect {}` delay the first `flow`
- To mitigate that, you can use `.buffer()`, `.conflate()`, or `.collectLatest {}`
- Example using `.buffer()`:
```kotlin
viewModelScope.launch {
	flow.onEach {
		println("FLOW: $it is delivered")
	}
	.buffer() // Insert buffer or conflate before collect
	.collect {
		println("FLOW: Now eating $it")
		delay(1500L)
		println("FLOW: Finished eating $it")
	}
}
```
- The `.buffer()` makes the second flow's `.collect {}` run on a different coroutine than the first flow, that way both flows can run simultaneously:
```
FLOW: Appetizer is delivered
FLOW: Now eating Appetizer
FLOW: Main dish is delivered
FLOW: Dessert is delivered
FLOW: Finished eating Appetizer
FLOW: Now eating Main dish
FLOW: Finished eating Main dish
FLOW: Now eating Dessert
FLOW: Finished eating Dessert
```
- The `.buffer()` will collect everything that is immediately ready
- Example using `.conflate()`:
```
FLOW: Appetizer is delivered
FLOW: Now eating Appetizer
FLOW: Main dish is delivered
FLOW: Dessert is delivered
FLOW: Finished eating Appetizer
FLOW: Now eating Dessert
FLOW: Finished eating Dessert
```
- The `.conflate()` explained:
	- If there are two emissions from the flow that we can't collect yet
		- Main dish and dessert are delivered, but we are still eating the appetizer
	- Then when we finish eating the appetizer
		- We go directly to the dessert since it was the latest emission that was started
		- Dinner gets skipped because dessert is the latest emission while appetizer was still running
- Now using `.collectLatest {}` instead of `.collect {}`:
```
FLOW: Appetizer is delivered
FLOW: Now eating Appetizer
FLOW: Main dish is delivered
FLOW: Now eating Main dish
FLOW: Dessert is delivered
FLOW: Now eating Dessert
FLOW: Finished eating Dessert
```
- The `.collectLatest {}` skips the "finished eating" for appetizer and main dish because a new dish was started in place of the current dish, but it will finish eating the dessert because there is no dish after it
