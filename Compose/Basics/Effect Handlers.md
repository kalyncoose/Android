---
tags:
  - android
  - done
  - compose
  - compose_basics
---
Video: https://www.youtube.com/watch?v=gxWcfz3V2QE&list=PLQkwcJG4YTCSpJ2NLhDTHhi6XBNfk9WiC&index=10
- "Side effects" in Compose are things that escape the scope of a Composable function: https://developer.android.com/jetpack/compose/side-effects
- Example side effect:
  ```kotlin
  // class...
  private var i = 0
  // override fun onCreate...
	  setContent {
		 var text by remember {
			 mutableStateOf("")
		 }
		 Button(onClick = { text += "#" }) {
			 i++ // side effect, not in compose state
			 Text(text = text)
		 }
	  }
	```
- More realistic side effect is triggering a network call inside a Composable
- Rule of thumb: avoid doing non-Compose things in Compose code
- But, if you really need to, effect handlers are the solution
- `LaunchedEffect`: https://developer.android.com/jetpack/compose/side-effects#launchedeffect
  ```kotlin
  var text by remember {
	  mutableStateOf("")
  }
  LaunchedEffect(key1 = text) {
	  delay(1000L)
	  println("The text is $text")
  }
	```
	- Most commonly used
	- Is actually a `@Composable` itself
	- Requires key(s) and a block
	- Block is a `CoroutineScope`
	- When a key changes, the coroutine is cancelled and re-launched with the new value
	- Example: need to run `sharedFlow.collect` inside a Composable but you don't want it to run on every re-composition (use `key = true` to ensure it runs only once)
	- Example: launch an animation and transition between an existing animation smoothly
- `rememberCoroutineScope`: https://developer.android.com/jetpack/compose/side-effects#remembercoroutinescope
  ```kotlin
  val scope = rememberCoroutineScope()
  Button(onClick = {
	  scope.launch {
		  delay(1000L)
		  println("Hello World!")
	  }
  })
	```
	- Create a coroutine scope that is aware of the composable state
	- If composable stops rendering then coroutine scope is cancelled
	- Only used in callbacks, like `onClick`
	- If you already have a `ViewModel` scope then this is not needed
	- Not used often
- `rememberUpdatedState`: https://developer.android.com/jetpack/compose/side-effects#rememberupdatedstate
  ```kotlin
  @Composable
  fun RememberUpdatedStateDemo(
	  onTimeout: () -> Unit
  ) {
	  val updatedOnTimeout by rememberUpdatedState(newValue = onTimeout)
	  LaunchedEffect(key1 = true) {
		  delay(3000L)
		  updatedOnTimeout()
	  }
  }
	```
	- Ensures an argument passed to a Composable is updated when used internally during side effect handling
	- Example: passing a timeout function to a `LaunchedEffect`, the timeout function would not be updated when a new one is passed as an argument unless we use `rememberUpdatedState`
	- Not used often
- `DisposableEffect`: https://developer.android.com/jetpack/compose/side-effects#disposableeffect
  ```kotlin
  @Composable
  fun DisposableEffectDemo() {
	  val lifecycleOwner = LocalLifecycleOwner.current
	  DisposableEffect(key1 = lifecycleOwner) {
		  val observer = LifecycleEventObserver { _, event -> 
			  if(event == Lifecycle.Event.ON_PAUSE) {
				  println("On pause called")
			  }
		  }
		  lifecycleOwner.lifecycle.addObserver(observer)
		  // Remove observer on dispose
		  onDispose {
			  lifecycleOwner.lifecycle.removeObserver(observer)
		  }
	  }
  }
	```
	- Example: need to remove a lifecycle event observer when a Composable gets removed, this cannot be done with a `LaunchedEffect`
	- Pretty commonly used
	- Prevents leaking of code in memory
	- Used commonly for things that need cleanup
- `SideEffect`: https://developer.android.com/jetpack/compose/side-effects#sideeffect-publish
  ```kotlin
  @Composable
  fun SideEffectDemo(nonComposeCounter: Int) {
	  SideEffect {
		  println("Called after every successful recomposition")
	  }
  }
	```
	- Runs whenever your Composable gets recomposed
	- Useful for running specific actions when any non-Compose state in your Composable changes
	- Example: update analytics for a Firebase user when a Composable runs with that user
	- Not used often
- `produceState`: https://developer.android.com/jetpack/compose/side-effects#producestate
  ```kotlin
  @Composable
  fun produceStateDemo(countUpTo: Int): State<Int> {
	  return produceState(initialValue = 0) { // similar to flow<Int> with emit(value)
		  while(value < countUpTo) {
			  delay(1000L)
			  value++
		  }
	  }
  }
	```
	- It's goal is to produce state that changes over time, so it is similar to a flow
	- Provides a coroutine scope similar to `LaunchedEffect`
	- Not used often
- `derivedStateOf`: https://developer.android.com/jetpack/compose/side-effects#derivedstateof
  ```kotlin
  @Composable
  fun DerivedStateOfDemo() {
	  var counter by remember {
		  mutableStateOf(0)
	  }
	  val counterText by derivedStateof {
		  "The counter is $counter"
	  }
	  Button(onClick = { counter++ }) {
		  Text(text = counterText)
	  }
  }
	```
	- If you have a complex thing in state that changes over time, then you want to use this
	- Caches pieces of state to be memory efficient
	- Simply allows you to memoize state
	- Not often used
- `snapshotFlow`: https://developer.android.com/jetpack/compose/side-effects#snapshotFlow
  ```kotlin
  @Composable
  fun SnapshotFlowDemo() {
	  val scaffoldState = rememberScaffoldState()
	  LaunchedEffect(key1 = scaffoldState) {
		  snapshotFlow { scaffoldState.snackbarHostState } // select the state
			  .mapNotNull { it.currentSnackbarData?.message } // state to watch
			  .distinctUntilChanged() // only emit if state actually changed
			  .collect { message ->
				  printlin("A snackbar with message $message was shown")
			  }
	  }
  }
	```
	- `snapshotFlow` is the opposite of a `flow.collectAsState()`
		- A `flow` collects into Composable state whereas `snapshotFlow` collects Compose state into a `flow` that emits values whenever a Compose state changes
	- Example: watch for a snackbar state changes and run logic based on the changed state
	- Not often used