---
tags:
  - android
  - done
  - compose
  - layout
---
Video: https://www.youtube.com/watch?v=FBpiOAiseD0&list=PLQkwcJG4YTCSpJ2NLhDTHhi6XBNfk9WiC&index=9
- A `ConstraintLayout` lets you create large, complex layouts with a flat view hierarchy meaning no nested view groups: 
	- Docs: https://developer.android.com/develop/ui/views/layout/constraint-layout
		- To define a view's position in `ConstraintLayout`, you add at least one horizontal and one vertical constraint for the view
		- Each constraint represents a connection or alignment to another view, the parent layout, or an invisible guideline
		- Each constraint defines the view's position along the vertical or horizontal axis
		- Each view must have a minimum of one constraint for each axis, but often more are necessary
- With the original XML version, you could use your mouse on a layout editor window to define views and their constraints with dragging and dropping
- To use `ConstraintLayout` in Compose, you must add a dependency:
	- `implementation "androidx.constraintlayout:constraintlayout-compose:1.0.1"`
	- https://developer.android.com/jetpack/compose/layouts/constraintlayout
- For simple things, use `Column`'s and `Row`'s in Compose, but for more complex UI designs you can use `ConstraintLayout` if desired
	- Look into Guidelines, Barriers, and Chains for advanced functionality
- Full example:
```kotlin
// ...
setContent {
	// Create a constraint set
	val constraints = ConstraintSet {
		val greenBox = createRefFor("greenbox")
		val redBox = createRefFor("redbox")
		val guideline = createGuidelineFromTop(0.5f)

		constrain(greenBox) {
			// Set greenBox to constrain to top-left of parent
			top.linkTo(guideline) // or top.linkTo(parent.top)
			start.linkTo(parent.start)

			// Set constraint sizes
			width = Dimension.value(100.dp)
			height = Dimension.value(100.dp)
		}
		constrain(redBox) {
			// Set redBox to start next to the greenBox
			top.linkTo(parent.top)
			start.linkTo(greenBox.end)
			end.linkTo(parent.end)

			// Set constraint sizes
			width = Dimension.value(100.dp) // or .fillToConstraints
			height = Dimension.value(100.dp)
		}
		// or createVerticalChain()
		createHorizontalChain(greenBox, redBox, chainStyle = ChainStyle.Packed) 
	}

	// Add the constraints to a constraint layout
	ConstraintLayout(constraints, modifier = Modifier.fillMaxSize()) {
		Box(
			modifier = Modifier
				.background(Color.Green)
				.layoutId("greenbox")
		)
		Box(
			modifier = Modifier
				.background(Color.Red)
				.layoutId("redbox")
		)
	}
}
```
