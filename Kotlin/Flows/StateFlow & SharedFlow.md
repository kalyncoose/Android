---
tags:
  - android
  - done
  - kotlin
  - flows
---
Video: https://www.youtube.com/watch?v=za-EEkqJLCQ&list=PLQkwcJG4YTCQHCppNAQmLsj_jW38rU9sC&index=3
- `StateFlow` is used to keep one single value of state
	- It's like `LiveData` but without the lifecycle awareness
	- e.g. `LiveData` knows when the app is in the background but `StateFlow` does not
	- Because it only modifies one value, it is considered a "hot flow", even with no collectors
- Example using `StateFlow`:
	- `private val _stateFlow = MutableStateFlow(0)`
		- The `_` prefix traditionally indicates a mutable flow
	- `val stateFlow = _stateFlow.asStateFlow()`
		- Now `stateFlow` is immutable as it only captures and represents the current mutable state flow using `.asStateFlow()`
	- Now we can increment the number in `stateFlow` like this:
```kotlin
fun incrementCounter() {  
	_stateFlow.value += 1  
}
```
- `StateFlow` is commonly used to retain UI state in the `ViewModel` for scenarios such as when an activity lifecycle changes (e.g. the device screen orientation updates)
	- However you don't really need `StateFlow` with Compose because Compose already comes with state
	- Sometimes will need it in Compose if another library returns a `StateFlow` that you need
	- To use `StateFlow` in an XML-based UI Android project you would need to use a much different pattern (see video at timestamp `6:47` for that example)
- When it comes to emissions there are essentially two types:
	- State emissions where the value actually persists (e.g. `StateFlow`)
		- Example: storing a counter value that persists between activity lifecycles
	- One-time emissions (e.g. `SharedFlow`)
		- Example: start a snackbar message or navigate to a screen once after login
- `SharedFlow` is used to send one-time events
	- Can be used with many collectors
	- Is also considered a "hot flow" because the value is lost if there are no collectors
	- Does not use a normal "flow builder" like a regular `flow {}`
	- Can be used at any place in the `ViewModel` to send an event at any time
	- Similar to channels but channels are designed to be only used with a single observer
- Example using `SharedFlow`:
```kotlin
// class MainViewModel: ViewModel() { ...
// Setup the shared flow state
private val _sharedFlow = MutableSharedFlow<Int>(0)  
val sharedFlow = _sharedFlow.asSharedFlow()

// Setup a function to emit an event onto the shared flow
fun squareNumber(number: Int) {
	viewModelScope.launch {
		// Emit will suspend until all collectors have processed the event
		_sharedFlow.emit(number * number)
	}
}

// Run multiple collectors that need to process the events
init {
	squareNumber(3)
	viewModelScope.launch {
		sharedFlow.collect {
			delay(2000L)
			println("FIRST FLOW: The received number is $it")
		}
	}
	viewModelScope.launch {
		sharedFlow.collect {
			delay(3000L)
			println("SECOND FLOW: The received number is $it")
		}
	}
}
```
- Returns the logs:
```
FIRST FLOW: The received number is 9
SECOND FLOW: The received number is 9
```
- Note that this is still a one-time event, but two collectors consume it
- Typically with `SharedFlow` you would like to use `sharedFlow.collect {}` instead of `.collectLatest {}` because usually you don't want to drop events
	- Example: if you are sending a snackbar event and then a navigation event, you would not want to skip the snackbar event
- Sometimes you may want to cache an emission from a `SharedFlow` for any potential new collectors (events that occur before a collector is created are lost by default)
	- To do this, you can specify `replay = Int`, e.g. `MutableSharedFlow<Int>(replay = 5)`
		- This sets the shared flow to cache 5 emissions
- Now how do you use shared flow in the UI?
	- You cannot use `.collectAsState()` on a shared flow
	- Use a `LaunchedEffect()` instead:
```kotlin
LaunchedEffect(key1 = true) {
	viewModel.sharedFlow.collect { number ->
		// Now you can use in Compose
	}
}
```
