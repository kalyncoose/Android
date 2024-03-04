---
tags:
  - android
  - remote_api
  - done
---
Articles: 
- https://medium.com/@imkuldeepsinghrai/api-calls-with-retrofit-in-android-kotlin-a-comprehensive-guide-e049e19deba9
- https://medium.com/@jeremy.leyvraz/kotlin-simplify-your-api-calls-with-elegance-with-retrofit-1be6da7adae4
Notes:
- Add internet permission to the `AndroidManifest.xml`:
```xml
<uses-permission android:name="android.permission.INTERNET" />
```
- Add Retrofit dependencies to the app `build.gradle`:
```kotlin
val retrofit_version = "2.9.0"
implementation("com.squareup.retrofit2:retrofit:$retrofit_version")
implementation("com.squareup.retrofit2:converter-gson:$retrofit_version")
```
- Create a `RetrofitClient` and `ApiClient` in the singleton pattern:
```kotlin
object RetrofitClient {
    private const val BASE_URL = "https://jsonplaceholder.typicode.com/"

    val retrofit: Retrofit by lazy {
        Retrofit.Builder()
            .baseUrl(BASE_URL)
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }
}

object ApiClient {
    val apiService: ApiService by lazy {
        RetrofitClient.retrofit.create(ApiService::class.java)
    }
}
```
- Create an `ApiService` interface with a `Post` data class that defines API endpoints and methods:
```kotlin
data class Post(
    val userId: Int,
    val id: Int,
    val title: String,
    val body: String
)

interface ApiService {
    @GET("posts/{id}")
    fun getPostById(@Path("id") postId: Int): Call<Post>
}
```
- Note: In the `Post` data class you could add  `@SerializedName` annotations to help the `gson` converter map the incoming JSON field name to your data class field name if they are different:
```kotlin
data class Post(
	@SerializedName("userId") // Incoming from API
    val accountId: Int, // Map to a new field name
    // ...
)
```
- Now you can call your API endpoint in the `MainActivity`:
```kotlin
Column(
	modifier = Modifier.fillMaxSize(),
	horizontalAlignment = Alignment.CenterHorizontally,
	verticalArrangement = Arrangement.spacedBy(16.dp)
) {
	val post = remember {
		mutableStateOf<Post?>(null)
	}
	Button(onClick = {
		// Note: Don't use GlobalScope, this is just for quick demonstration
		GlobalScope.launch {
			try {
				val response = ApiClient.apiService.getPostById(1).awaitResponse()
				Log.d("RETROFIT", "Success: ${response.isSuccessful}")
				if (response.isSuccessful) {
					Log.d("RETROFIT", "Response Body: ${response.body()}")
					post.value = response.body()
				}
			} catch (e: Exception) {
				Log.d("RETROFIT", e.toString())
			}
		}

	}) {
		Text(text = "Get Post By ID: 1")
	}
	Text(text = "Post = ${post.value}")
}
```
- Example output logs:
```Log
02:27:56.350  D  Success: true
02:27:56.350  D  Response Body: Post(userId=1, id=1, title=sunt aut facere repellat provident occaecati excepturi optio reprehenderit, body=quia et suscipit
                 suscipit recusandae consequuntur expedita et cum
                 reprehenderit molestiae ut ut quas totam
                 nostrum rerum est autem sunt rem eveniet architecto)
```
