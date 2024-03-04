---
tags:
  - android
  - done
  - kotlin
  - flows
---
Video: https://www.youtube.com/watch?v=ZX8VsqNO_Ss&list=PLQkwcJG4YTCQHCppNAQmLsj_jW38rU9sC
- A `Flow` is a coroutine that can emit multiple values over a period of time
	- Allows you to notify of changes during runtime and react to them
- Example: Count down timer flow
  ```kotlin
  // class MainViewModel: ViewModel() { ...
  val countDownFlow = flow<Int> { // Create the flow, emits Int types
	val startingValue = 10
	var currentValue = startingValue
	emit(startingValue) // Emit the starting value
	while (currentValue > 0) {
		delay(1000L)
		currentValue--
		emit(currentValue) // Emit the current value
	}
  }
  
  // MainActivity...
  setContent {
	val viewModel = viewModel<MainViewModel>()
	val time = viewModel.countDownFlow.collectAsState(initial = 10) // Get the state
	Box(modifier = Modifier.fillMaxSize()) {
		Text(
			text = time.value.toString(), // Show the live counter
			fontSize = 30.sp,
			modifier = Modifier.align(Alignment.Center)
		)
	}
  }
	```
- In this example the `flow<Int>` is a "cold flow" because there are no collectors
- `flow.collectAsState()` collects the flow's state into a useable state variable
- `flow.collect {}` collects all of the flow's emissions
- `flow.collectLatest {}` collects the flow's emissions but cancels the current block if a new emission comes in before the old one finishes
- Instead of using `flow.collect` you could use this pattern instead:
  ```kotlin
  countDownFlow.onEach {
	  println(it)
  }.launchIn(viewModelScope)
```
