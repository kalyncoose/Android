---
tags:
  - android
  - clean_code
  - done
---
Video: https://www.youtube.com/watch?v=hSgPNbEcX98
- Add dependency:
```kotlin
plugins {
	id "org.jlleitschuh.gradle.ktlint" version "11.0.0"
}
```
- Add `ktlint` block to your app's `build.gradle`:
```kotlin
ktlint {
	android = true
	ignoreFailures = false
	disabledRules = ["final-newline", "no-wildcard-imports"]
	reporters {
		reporter "plain"
		reporter "checkstyle"
		reporter "sarif"
	}
}
```
- Commands:
	- `./gradlew ktlintCheck` - Run linter
	- `./gradlew ktlintFormat` - Auto-fix issues
- Add line to automatically run `ktlintFormat` task before Gradle builds:
```kotlin
gradle.getByPath("preBuild").dependsOn("ktlintFormat")
```
- Optional: Setup reformat on save macro
	- https://medium.com/nerd-for-tech/auto-format-code-in-android-studio-intellij-idea-1f0450ee44a3
- Optional: Ktlint plugin
	- https://plugins.jetbrains.com/plugin/15057-ktlint
- Ktlint Docs: https://pinterest.github.io/ktlint/latest/
