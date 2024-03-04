---
tags:
  - android
  - dependency_injection
  - done
---
Video: https://www.youtube.com/watch?v=bbMsuI2p1DQ
- Dependency injection is a design pattern
	- Simply put: give an object its instance variables
- Hilt is a library from Google is a simpler version of Dagger 2 (built on top of) to use dependency injection
- Helps control interfaces for API and Room service for example, as well as lifetime of the dependencies like using the singleton pattern
- See docs for Gradle plugin/dependency/kapt/compiler configuration: https://developer.android.com/training/dependency-injection/hilt-android
	- Additionally need these:
```kotlin
implementation("androidx.hilt:hilt-lifecycle-viewmodel:1.0.0-alpha03")  
kapt("androidx.hilt:hilt-compiler:1.2.0")  
implementation("androidx.hilt:hilt-navigation-compose:1.2.0")
```
- Create a `MyApi` interface at `data/remote/`:
```kotlin
// Contract for the remote API (lives under data)
interface MyApi {
    @GET("test")
    suspend fun doNetworkCall()
}
```
- Create a `MyRepository` interface at `domain/repository/`:
```kotlin
// Contract for the repository to implement (lives under domain)
interface MyRepository {
    suspend fun doNetworkCall()
}
```
- Create a `MyRepositoryImpl` class at `data/repository/`:
```kotlin
// Implementation of the repository contract (lives under data)
class MyRepositoryImpl(
    private val api: MyApi
): MyRepository {
    override suspend fun doNetworkCall() {
        TODO("Not yet implemented")
    }
}
```
- Now to implement Dagger-Hilt, we need to create a module for `MyApi` so it can be injected into the `MyRepositoryImpl` implementation class
	- Create separate modules for individual dependencies like a remote API, web sockets, auth repository, etc.
	- Can create modules to have a designated lifecycle, like app or activity
	- Ensure modules have specific and clear responsibilities or scopes
- Create a `di` package in the root of the project
	- This will store the Hilt modules for the app
- Create an `AppModule` object at `di/`:
```kotlin
@Module
@InstallIn(SingletonComponent::class)
object AppModule {
    // ...
}
```
- The `@Module` annotation tells Hilt that this object is a Hilt module
- The `@InstallIn()` annotation tells Hilt what lifecycle component should the Hilt module be attached to, examples:
	- `SingletonComponent` lives as long as the app
	- `ActivityComponent` lives as long as an activity
	- `ViewModelComponent` lives as long as a `ViewModel`
	- `ActivityRetainedComponent` lives as long as an activity but is retained through activity lifecycles like screen rotation
	- `ServiceComponent` lives as long as an Android service
- Define a `provideMyApi` function inside the `AppModule` object:
```kotlin
@Provides
@Singleton
fun provideMyApi(): MyApi {
	// Provide retrofit as MyApi
	return Retrofit.Builder()
		.baseUrl("https://test.com")
		.build()
		.create(MyApi::class.java)
}
```
- The `@Provides` annotation tells Hilt that this function provides a dependency
- The `@Singleton` annotation tells Hilt that the dependency here should implement a singleton object pattern (single instance so multiple objects are never created at the same time)
- These provider functions are never called by you, but rather Hilt will use them automatically
- Create a `MyViewModel` class at `presentation/`:
```kotlin
@HiltViewModel
class MyViewModel @Inject constructor(
    private val repository: MyRepository
): ViewModel() {
    // ...
}
```
- The `@HiltViewModel` annotation tells Hilt that the class below is a `ViewModel` and should match its component lifecycle
- The `@Inject` annotation before `constructor` tells Hilt to look for any dependencies needed in the `ViewModel` and automatically connect them together
- Create a `provideMyRepository` function inside the `AppModule` object:
```kotlin
@Provides
@Singleton
fun provideMyRepository(api: MyApi): MyRepository {
	// Provide MyRepositoryImpl as MyRepository
	return MyRepositoryImpl(api)
}
```
- Since `MyApi` is a dependency for `MyRepository`, Hilt will see that and automatically connect the correct instance of `MyApi` to it
- Now you can use `MyViewModel` inside the `MainActivity`:
```kotlin
val viewModel = hiltViewModel<MyViewModel>()
```
- The `hiltViewModel` function is special for the `ViewModel` to have dependencies injected
- Add `@AndroidEntryPoint` annotation to the top of `MainActivity` so that Hilt knows where the app's entry point is:
```kotlin
@AndroidEntryPoint
class MainActivity : ComponentActivity() {
	// ...
}
```
- Create a `MyApp` class in the root package:
```kotlin
@HiltAndroidApp
class MyApp: Application()
```
- The `@HiltAndroidApp` annotation tells Hilt about your app so it can use `Application` context
- Now register `MyApp` as the application in the `AndroidManifest`:
```xml
<application android:name=".MyApp" ...>
```
- Add `private val appContext: Application` to the `MyRepositoryImpl` class signature
- Add `app` and pass it to `MyRepositoryImpl` inside `provideMyRepository` function in `AppModule`:
```kotlin
@Provides
@Singleton
fun provideMyRepository(api: MyApi, app: Application): MyRepository {
	// Provide MyRepositoryImpl as MyRepository
	return MyRepositoryImpl(api, app)
}
```
- Now the application context is connected from the `MainActivity` to the Hilt module
- To use the application context, you can get the app name resource string from an `init` inside the `MyRepositoryImpl` class for example:
```kotlin
init {
	val appName = appContext.getString(R.string.app_name)
	println("MyRepository: appName=${appName}")
}
```
- Example of deconflicting two dependencies of the same type in `AppModule`:
```kotlin
@Provides
@Singleton
@Named("hello1")
fun provideString1() = "Hello 1"

@Provides
@Singleton
@Named("hello2")
fun provideString2() = "Hello 2"

// Now you can use "hello1" in another provider
@Provides
@Singleton
fun provideSomething(
	@Named("hello1") hello1: String
	// other deps...
) {
	// ...
}
```
- The `@Named(String)` annotation tells Hilt how to differentiate between two dependencies of the same type
- Remove `provideMyRepository` function in `AppModule` and create a `RepositoryModule` class at `di/`:
```kotlin
@Module
@InstallIn(SingletonComponent::class)
abstract class RepositoryModule {

    // Bind dependency instead of provide
    @Binds
    @Singleton
    abstract fun bindMyRepository(
        myRepositoryImpl: MyRepositoryImpl
    ): MyRepository
}
```
- This will work the way as `@Provides` function but instead generates less code when building the app since `@Binds` annotation is a simpler way to tell Hilt to use your implementation function
- Now you need to update `MyRepositoryImpl` function to use `@Inject constructor()`
- Example of field injection, create a `MyService` class at the root package:
```kotlin
@AndroidEntryPoint
class MyService: Service() {
    
    // Needs to be public
    @Inject
    lateinit var repository: MyRepository

    override fun onCreate() {
        super.onCreate()
        // Use dependency after super.onCreate(), for example:
        GlobalScope.launch {
            repository.doNetworkCall()
        }
    }

    override fun onBind(p0: Intent?): IBinder? {
        TODO("Not yet implemented")
    }
}
```
- Uses `@Inject` on a `lateinit var` instead, which gets instantiated during `super.onCreate()`
	- This pattern of field injection is useful for when you cannot use the `@Inject constructor()` pattern (in this case `Service()` does not use `constructor()`)
	- Ensure the `lateinit var` is public for Hilt to inject into it
	- Only use the injected dependencies after `super.onCreate()` because that is when Hilt is done injecting them
- Example of lazy injection:
```kotlin
@HiltViewModel
class MyViewModel @Inject constructor(
    private val repository: Lazy<MyRepository> // Use Lazy<> with dependency type
): ViewModel() {
    // ...
}
```
- Lazy injection is when you inject a dependency and delay the creation of a component
	- Normally a dependency is constructed as soon as it is injected
	- Lazy injection only constructs the dependency when it is actually used
	- Useful in certain scenarios, for example an API uses an interceptor and requires an auth token that is not known at compile time, so you can use lazy injection to only create the dependency when the token is first available during runtime
