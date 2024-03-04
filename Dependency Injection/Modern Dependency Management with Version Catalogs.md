---
tags:
  - android
  - dependency_injection
  - done
---
Video: https://www.youtube.com/watch?v=MWw1jcwPK3Q
- Gradle "Version Catalogs" come standard in new Android Studio - Iguana version
- You now use a separate file: `libs.versions.toml`:
```toml
[versions]
...
[libraries]
...
[plugins]
...
```
- In `[versions]` you store version variables that can be used in both `[libraries]` and `[plugins]`
	- Example: `activity-compose = "1.8.2"`
- In `[libraries]` you store the dependencies that your app uses
	- Example: `androidx-activity-compose = { module "androidx.activity:activity-compose", version-ref = "activity-compose" }`
- In `[plugins]` you store Gradle plugins required for your app
	- Example: `androidApplication = { id = "com.android.application", version.ref = "agp" }`
- Then once your libraries are added, you can reference them in a Gradle file like so:
```kotlin
dependencies {
	implementation(libs.androidx.activity.compose)
	// ...
}
```
- Then once your plugins are added, you can reference them in a Gradle file like so:
```kotlin
plugins {
	alias(libs.plugins.androidApplication)
	// ...
}
```
