---
tags:
  - android
  - done
  - kotlin
  - flows
---
Videos: https://www.youtube.com/watch?v=JAjcSVmVmos&list=PLQkwcJG4YTCQHCppNAQmLsj_jW38rU9sC&index=5
- `.combine()`, `.zip()`, `.merge()` are useful Kotlin Flow operators
- For the example below, there will be these three data classes:
```kotlin
data class User(  
	val username: String? = null,  
	val description: String? = null,  
	val profilePicUrl: String? = null  
)

data class Post(  
	val imageUrl: String? = null,  
	val username: String? = null,  
	val description: String? = null  
)

data class ProfileState(
    val profilePicUrl: String? = null,
    val username: String? = null,
    val description: String? =  null,
    val posts: List<Post> = emptyList()
)
```
- Example using `.combine()` on 3 different flows:
```kotlin
class MainViewModel: ViewModel() {

    private val isAuthenticated = MutableStateFlow(true)
    private val user = MutableStateFlow<User?>(null)
    private val posts = MutableStateFlow(emptyList<Post>())

    private val _profileState = MutableStateFlow<ProfileState?>(null)
    private val profileState = _profileState.asStateFlow()

    init {
	    // Combine the isAuthenticated flow with user flow
        isAuthenticated.combine(user) { isAuthenticated, user ->
	        // Pass the user downstream if isAuthenticated=true
            if(isAuthenticated) user else null
        }.combine(posts) { user, posts -> // Combine the previous flow with posts flow
            user?.let { // If user was passed down
	            // Update profile state flow
                _profileState.value = profileState.value?.copy(
                    profilePicUrl = user.profilePicUrl,
                    username = user.username,
                    description = user.description,
                    posts = posts
                )
            }
        }.launchIn(viewModelScope) // Launch the flow in view model's coroutine scope
    }
}
```
- `.combine()` is called when either the first flow's or the second flow's state changes
- Example using `.zip()` on two different flows:
```kotlin
class MainViewModel: ViewModel() {

    private val flow1 = (1..10).asFlow().onEach { delay(1000L) }
    private val flow2 = (10..20).asFlow().onEach { delay(300L) }
    var numberString by mutableStateOf("")
        private set

    init {
        flow1.zip(flow2) { number1, number2 ->
            numberString += "($number1, $number2)\n"
        }.launchIn(viewModelScope)
    }
}
```
- `.zip()` will wait for emissions of both flows before running the coroutine logic
- Example using `.merge()` on multiple flows:
```kotlin
class MainViewModel: ViewModel() {

    private val flow1 = (1..10).asFlow().onEach { delay(1000L) }
    private val flow2 = (10..20).asFlow().onEach { delay(300L) }
    var numberString by mutableStateOf("")
        private set

    init {
        merge(flow1, flow2).onEach {
            numberString += "$it\n"
        }.launchIn(viewModelScope)
    }
}
```
- `.merge()` takes in any number of flows, but they need to be of the same data type, does not wait on any other flow's emission like `.zip()` so it's more similar to `.combine()`
