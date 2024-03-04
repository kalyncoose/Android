---
tags:
  - android
  - done
  - compose
  - material3
---
Video: https://www.youtube.com/watch?v=ZERIxmBYP-U
- Two pre-made style options:
	- Filled
	- Outlined
- As a general rule, you do not want to mix different styles of text fields in the same screen or even in the same app
- Gradle Build
```kotlin
// Pulls latest stable release, unspecified version
implementation("androidx.compose.material3:material3")
// Add icons if desired
implementation("androidx.compose.material:material-icons-extended:1.6.2")
```
- `enabled` vs `readonly`
	- If a text field is `readonly` the user is able to highlight and copy the text, but if it is `enabled=false` then they cannot highlight or copy text
- Use `LocalTextStyle.current.copy()` to retain same styling with minimal changes:
```kotlin
textStyle = LocalTextStyle.current.copy(  
	textAlign = TextAlign.Right  
)
```
- Example:
```kotlin
var filledText by remember {
	mutableStateOf("")
}
TextField(
	value = filledText,
	onValueChange = { filledText = it },
	textStyle = LocalTextStyle.current.copy(
		textAlign = TextAlign.Right
	),
	label = {
		Text(text = "Enter your wieght")
	},
	placeholder = {
		Text(text = "Weight")
	},
	leadingIcon = {
		Icon(
			imageVector = Icons.Outlined.MonitorWeight,
			contentDescription = "Weight"
		)
	},
	suffix = {
		Text(text = "kg")
	},
	supportingText = {
		Text(text = "Please choose a real weight")
	},
	isError = false,
	singleLine = true,
	keyboardOptions = KeyboardOptions(
		// Set the keyboard type for a template set of keys
		keyboardType = KeyboardType.Decimal,
		// Customize what the main action button of the keyboard is
		imeAction = ImeAction.Next
	),
	// Add custom logic to the keyboard
	keyboardActions = KeyboardActions(
		onNext = {
			println("Switch to the next page or something")
		}
	)
)
```
- Can use `visualTransformation` to set the field to a password mode:
	- e.g. `visualTransformation = PasswordVisualTransformation()`