---
tags:
  - android
  - done
  - compose
  - material3
---
Video: https://www.youtube.com/watch?v=2y4XiyJv0pQ
- Buttons come with guidelines on how to use them: https://m3.material.io/components/buttons/overview
	- As a general rule, follow material guidelines and don't modify button shapes or colors away from the defaults/theme unless you are doing a custom design system opposed from material
- Filled Button
	- High visual impact, suitable for important actions.
	- Container surrounds text label with consistent padding.
	- Container size can be responsive to layout grid.
	- Containers should have solid color.
	- Can be used for final actions completing a flow.
```kotlin
Button(
	onClick = { /*TODO*/ }
) {
	Text(text = "Confirm")
}
```
- Elevated Button
	- Filled tonal buttons with shadow for visual separation.
	- Use when visual separation from patterned background is necessary.
	- Higher elevation increases prominence in design.
```kotlin
ElevatedButton(
	onClick = { /*TODO*/ }
) {
	Icon(
		imageVector = Icons.Filled.Add,
		contentDescription = "Add to cart",
		modifier = Modifier.size(18.dp)
	)
	Spacer(modifier = Modifier.width(8.dp))
	Text(text = "Add to cart")
}
```
- Filled Tonal Button
	- Middle ground between filled and outlined buttons.
	- Used for lower-priority actions needing slight emphasis.
	- Utilize secondary color mapping.
```kotlin
FilledTonalButton(
	onClick = { /*TODO*/ }
) {
	Text(text = "Open in browser")
}
```
- Outlined Button
	- Medium-emphasis buttons for important but not primary actions.
	- Pair well with filled buttons to indicate alternative actions.
	- Display stroke around button container with no fill by default.
	- Can be placed on various backgrounds, but customization may be needed for legibility.
	- Caution advised when placing on top of images.
```kotlin
OutlinedButton(
	onClick = { /*TODO*/ }
) {
	Text(text = "Back")
}
```
- **Text Button**
	- Depend on color and style of label text for recognition.
	- Label text should contrast with other elements.
	- Containers are visible only on hover, focus, or press.
	- Width should accommodate label text without overflow.
	- Icons are optional and should be placed before the label text.
	- Used for low-priority actions, embedded in components like cards, dialogs, and snackbars.
	- Absence of visible container in default state reduces distraction.
	- Suitable for maintaining emphasis on content in cards.
	- Aligned to the ending edge of dialogs.
```kotlin
TextButton(
	onClick = { /*TODO*/ }
) {
	Text(text = "Learn more")
}
```
 