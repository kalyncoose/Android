---
tags:
  - android
  - done
  - compose
  - compose_examples
---
Video: https://www.youtube.com/watch?v=_yON9d9if6g&list=PLQkwcJG4YTCSpJ2NLhDTHhi6XBNfk9WiC&index=7
- A `Scaffold` is a way to organize existing Composables in material design
	- Easily create a top bar, tool bar, navigation drawer, display snackbars, etc.
- `TextField` and `OutlinedTextField` are for material design text fields
- `BasicTextField` is for if you want to style a custom text field without material design
- `TextField`'s `value` stores the state of the text field
	- Where `textFieldState.value` is accessible:
	  ```kotlin
	  val textFieldState = remember {  
		  mutableStateOf("")  
	  }
		```
	- Where `textFieldState` is directly the value:
	  ```kotlin
	  var textFieldState by remember {  
		  mutableStateOf("")  
	  }
		```
- Tell the `Textfield` to update the state variable when value changes:
  ```kotlin
  onValueChange = {  
	  textFieldState = it  
  }
	```
- To launch a snackbar within a `Scaffold` you need to define a host and state:
	- State: `val snackbarHostState = remember { SnackbarHostState() }`
	- Host: `snackbarHost = { SnackbarHost(hostState = snackbarHostState)  },`
- Create state and a coroutine scope for the snackbar to launch in:
	- State:  `val scope = rememberCoroutineScope()`
	- Scope: `scope.launch { snackbarHostState.showSnackbar("Hello $textFieldState") }`
		- Goes within a button `onClick`
- Full example:
  ```kotlin
// ...setContent {
val scope = rememberCoroutineScope()  
val snackbarHostState = remember { SnackbarHostState() }  
var textFieldState by remember {  
	mutableStateOf("")  
} 
  
Scaffold(  
	modifier = Modifier.fillMaxSize(),  
	snackbarHost = {  
		SnackbarHost(hostState = snackbarHostState)  
	},  
) { _ ->  
	Column(  
		horizontalAlignment = Alignment.CenterHorizontally,  
		verticalArrangement = Arrangement.Center,  
		modifier = Modifier  
			.fillMaxSize()  
			.padding(horizontal = 30.dp)  
	) {  
		TextField(  
			value = textFieldState,  
			label = {  
				Text(text = "Enter your name")  
			},  
			onValueChange = {  
				textFieldState = it  
			},  
			singleLine = true,  
			modifier = Modifier.fillMaxWidth()  
		)  
		Spacer(modifier = Modifier.height(16.dp))  
		Button(onClick = {  
			scope.launch {  
				snackbarHostState.showSnackbar("Hello $textFieldState")  
			}  
		}) {  
			Text(text = "Submit")  
		}  
	}  
}
	```
