---
tags:
  - android
  - done
  - kotlin
  - coroutines
---
Video: https://www.youtube.com/watch?v=uiPYcSSjNTI&list=PLQkwcJG4YTCQcFEPuYGuv54nYai_lwil_&index=8
- The `GlobalScope` lives as long as the whole application does
	- This is usually bad practice to run coroutines in because you rarely need coroutines to last as long as the application
	- Can create memory leaks because a coroutine can hold onto resources that the garbage collector of an activity would otherwise destroy
- `lifecycleScope` and `viewModelScope` are more commonly used coroutine scopes
- May need dependencies in your `build.gradle` to use:
```kotlin
implementation "androidx.lifecycle:lifecycle-viewmodel-ktx:$arch_version" implementation "androidx.lifecycle:lifecycle-runtime-ktx:$arch_version"
```
- `lifecycleScope` keeps coroutines alive while the activity is alive
	- When activity is destroyed then all coroutines are also destroyed
- `viewModelScope` keeps coroutines alive while the view model is alive
	- When view model is destroyed then all coroutines are also destroyed