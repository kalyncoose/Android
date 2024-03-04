---
tags:
  - android
  - compose
  - material3
  - done
---
Video: https://www.youtube.com/watch?v=NYWTQZODr74
- General rules:
	- Use checkboxes when all options are related and a select all can be an option
	- Use switches for isolated options that are unrelated to other selections
		- Logic for the switch runs immediately so user sees instant effect of the change
	- Use radio buttons for forcing user to choose only one out of given options
- **Checkboxes**:
	- Compose only provides the box
	- Need to create your own label and state for the checkboxes manually
	- Define a data class to store checkbox info:
	```kotlin
	data class ToggleableInfo(
		val isChecked: Boolean,
		val text: String
	)
	```
	- Basic list of checkboxes and state example:
	  ```kotlin
	@Composable
	private fun Checkboxes() {
		val checkboxes = remember {
			mutableStateListOf(
				ToggleableInfo(
					isChecked = false,
					text = "Photos"
				),
				ToggleableInfo(
					isChecked = false,
					text = "Videos"
				),
				ToggleableInfo(
					isChecked = false,
					text = "Audio"
				)
			)
		}
	
		checkboxes.forEachIndexed { index, info ->
			Row(
				verticalAlignment = Alignment.CenterVertically
			) {
				Checkbox(
					checked = info.isChecked,
					onCheckedChange = { isChecked ->
						checkboxes[index] = info.copy(
							isChecked = isChecked
						)
					}
				)
				Text(text = info.text)
			}
		}
	}

		```
	- Advanced list of checkboxes with tristate example:
	  ```kotlin
	@Composable
	private fun Checkboxes() {
	    val checkboxes = remember {
	        mutableStateListOf(
	            ToggleableInfo(
	                isChecked = false,
	                text = "Photos"
	            ),
	            ToggleableInfo(
	                isChecked = false,
	                text = "Videos"
	            ),
	            ToggleableInfo(
	                isChecked = false,
	                text = "Audio"
	            )
	        )
	    }
	
	    var triState by remember {
	        mutableStateOf(ToggleableState.Indeterminate)
	    }
	    val toggleTriState = {
	        triState = when(triState) {
	            ToggleableState.Indeterminate -> ToggleableState.On
	            ToggleableState.On -> ToggleableState.Off
	            else -> ToggleableState.On
	        }
	        checkboxes.indices.forEach { index ->
	            checkboxes[index] = checkboxes[index].copy(
	                isChecked = triState == ToggleableState.On
	            )
	        }
	    }
	
	    Row(
	        verticalAlignment = Alignment.CenterVertically,
	        modifier = Modifier.clickable {
	            toggleTriState()
	        }.padding(end = 16.dp)
	    ) {
	        TriStateCheckbox(
	            state = triState,
	            onClick = toggleTriState
	        )
	        Text(text = "File types")
	    }
	
	    checkboxes.forEachIndexed { index, info ->
	        Row(
	            verticalAlignment = Alignment.CenterVertically,
	            modifier = Modifier
	                .padding(start = 32.dp)
	                .clickable {
	                    checkboxes[index] = info.copy(
	                        isChecked = !info.isChecked
	                    )
	                }.padding(end = 16.dp)
	        ) {
	            Checkbox(
	                checked = info.isChecked,
	                onCheckedChange = { isChecked ->
	                    checkboxes[index] = info.copy(
	                        isChecked = isChecked
	                    )
	                }
	            )
	            Text(text = info.text)
	        }
	    }
	}
		```
- **Switches**:
	- Example of custom switch:
```kotlin
	@Composable
	private fun MySwitch() {
	    var switch by remember {
	        mutableStateOf(
	            ToggleableInfo(
	                isChecked = false,
	                text = "Dark mode"
	            )
	        )
	    }
	
	    Row(
	        verticalAlignment = Alignment.CenterVertically
	    ) {
	        Text(text = switch.text)
	        Spacer(modifier = Modifier.weight(1f))
	        Switch(
	            checked = switch.isChecked,
	            onCheckedChange = { isChecked ->
	                switch = switch.copy(isChecked = isChecked)
	            },
	            thumbContent = {
	                Icon(
	                    imageVector = if(switch.isChecked) Icons.Default.Check else Icons.Default.Close,
	                    contentDescription = "Dark mode"
	                )
	            }
	        )
	    }
	}
```
- **Radio Buttons**:
	- Example of custom radio buttons:
```kotlin
	@Composable
	private fun RadioButtons() {
	    val radioButtons = remember {
	        mutableStateListOf(
	            ToggleableInfo(
	                isChecked = true,
	                text = "Photos"
	            ),
	            ToggleableInfo(
	                isChecked = false,
	                text = "Videos"
	            ),
	            ToggleableInfo(
	                isChecked = false,
	                text = "Audio"
	            )
	        )
	    }
	
	    radioButtons.forEachIndexed { index, info ->
	        Row(
	            verticalAlignment = Alignment.CenterVertically,
	            modifier = Modifier
	                .clickable {
	                    radioButtons.replaceAll {
	                        it.copy(
	                            isChecked = it.text == info.text
	                        )
	                    }
	                }
	                .padding(end = 16.dp)
	        ) {
	            RadioButton(
	                selected = info.isChecked,
	                onClick = {
	                    radioButtons.replaceAll {
	                        it.copy(
	                            isChecked = it.text == info.text
	                        )
	                    }
	                }
	            )
	            Text(text = info.text)
	        }
	    }
	}
```
