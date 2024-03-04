---
tags:
  - android
  - done
  - kotlin
---
- Developed by JetBrains, announced in 2011, released v1 in 2016
- Based on Java, meant to be simplified and modern, interoperable with Java and JVM
- Syntax and Usage
	- Easier and shortened syntax than Java
	- `fun` instead of `function`
	- Type inferencing
	- Can leave out semi-colons but now requires separate lines
	- `val` is a read-only constant (think `val` for `value`)
		- e.g. `val firstName = "John"`
		- e.g. `val firstName: String;`
	- `var` is a read/write variable (think `var` for `variable`)
	- String interpolation with `$` in normal `println`
	- Collections are **immutable**, all elements must be the same type
		- `val names = listOf("Ali", "Bob", "Jack")`
		- `listOf<String>("Ali", "Bob", "Jack")`
		- `mutableListOf("Ali", "Bob", "Jack")`
		- Note: `arrayOf()` is mutable whereas `listOf()` are immutable
	- For Loop
		- `for (name in names) { println(name) }`
		- `for (i in 1..5) { println(i) } // inclusive`
		- `for (i in until 5) { println(i) } // exclusive`
	- Nullability
		- Add `?` for nullability to variables
		- `val instagramBio: String? = "growth"`
		- Check for null with `if` statement or shorthand:
			- `instagramBio?.toUpperCase() // only applies func if not null`
	- All basic arithmetic, string operations, and logical expressions are the same as Java
	- Decimal numbers are `Float` type
		- e.g. `var x: Float = 20F`
		- Is the same as `var x = 20F` as the compiler recognizes `F` as `Float`
	- Double is more precise that `Float` type
		- e.g. `var x = 20.0`
		- Just omit the `F` and use `.0` to tell the compiler to use `Double` type
	- You can use `var result = readLine()` to take in a keyboard input from the running program similar to Python's `result = input()`
	- `Unit` return type is like a `void` in Java
		- Example: `fun returnsNothing(): Unit { ... }`
	- Switch Case
		- Use `when()` keyword:
		```kotlin
		when(age) {
			in 0..12 -> println("You're a kid")
			in 13..17 -> println("You're a teenager")
			18 -> println("You're eighteen")
			19, 20 -> println("You're nineteen or twenty")
			else -> { 
				// Can use curly braces for its own block
				println("You're an adult") 
			}
		}
		```
	- Function Syntax
	  ```kotlin
	  fun myFunction(param1: Int, param2: Int): Int {
		  // logic...
		  return 0
	  }
		```
	- Short Function Syntax
	  ```kotlin
	  fun multiply(a: Int, b:Int) = a * b
	```
	- Variable Arguments
		- Use `vararg` keyword
		- e.g. `fun getMax(vararg numbers: Int): Int { ... }`
	- Named Arguments
		- e.g. `fun searchFor(search: String, searchEngine: String) { ... }`
		- Use in any order with named arguments: 
			- `searchFor(searchEngine = "Bing", search = "How to do something")`
	- Extension Functions
		- Extend an existing type to have a new function
		- e.g. `fun Int.isPrime(number: Int): Boolean { ... }
	- Classes
		- Use classes for OOP
		  ```kotlin
		  // Define arguments for the constructor
		  class Rectangle(
			  val a: Double,
			  val b: Double
		  ) {
			  // Set class variables
			  val pi = 3.14
			  
			  // init serves as the constructor
			  init {
				  println("Rectangle created with $a and $b")
			  }
			  
			  fun area() = a * b
			  fun permiter() = 0 // ...
			  fun isSquare() = true // ...
		  }
		  
		  // Usage
		  val myRect = Rectangle(4.0, 7.0)
		  println("Area = ${myRect.area()}") // etc.
			```
	- Inheritance
		- Use `open` modifier to allow inheritance from a class
		- e.g. `open class Shape(...) { ... }`
		- Inheritance works similar to Java, just requires less code in some cases
	- Visibility
		- Use `private` modifier to ensure classes/variables/functions are limited to their current scope
		- The `public` modifier is redundant because everything is `public` by default
		- Use `private constructor` in class signature to also keep constructor private
			- e.g. `class Circle private constructor(...) { ... }`
		- Also `protected` and `internal` modifiers exist but are far less used
			- `protected` means item is not available for top-level declarations
				- Works similar to Java but has subtle difference
			- `internal` means item is visible everywhere in the same module
		- `private set` modifier means the setter for the variable is private to its scope
	- Abstract Classes
		- Use `abstract` modifier on class and functions
		- Works the same as Java
		- Now classes that implement the abstract class must use `override` keyword on their functions
			- `e.g. override fun area() = a * b`
	- Multiple Constructors
		- Use `constructor(...) : this(...)` format to have multiple constructors in a class
	- Function Overloading
		- Simply write the same function name but with different set of arguments
		- `e.g. fun hello(arg1, arg2)` and `fun hello(arg1, arg2, arg3)`
	- Objects
		- Use `object` keyword
		- Singleton pattern of a class
		- e.g. `object Number { ... }`
	- Companion Objects
		- Use `companion object { ... }` syntax inside a class
		- Implement singleton object behavior within a non-singleton class
	- Anonymous Class
		- Inherit most of an abstract class and change some functionality on the fly
		- Use  `val thing = object : SomeClass(...) { // custom logic... }` syntax
- Exceptions
	- Use `try`, `catch`, `finally` and `throw` keywords just like in Java
	- Create a custom exception using `class MyException : Exception(...)`
- Lambdas
	- Use `{ }` instead of `()` after a function name to invoke a lambda
	- `e.g. list = list.filter { it % 2 == 0 }`
	- Automatically returns the result of the expression written in the lambda
	- `it` is the default name of the given variable inside a lambda (what the function provides)
- Generics
	- Provide typing to a class
	- e.g. `List<Shape>`
	- When allowing generics in your functions use `<T>` syntax like:
		- `fun <T> List<T>.customFilter(...): List<T> { ... }`
	- Use `variableName::class` to identify the class type of the variable
	- Can be more specific with generic types like `<T : Number>`
