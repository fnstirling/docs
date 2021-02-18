# Coroutines

Coroutines is a mechanism for suspending functions and resuming them later in time, allowing for parallel computation.
Coroutines require two things to be created. A builder which creates a coroutine based on a configuration and a scope which gives the coroutine some lifecycle to follow.

A builder is just a function to launch and build a coroutine. There are different builders such as:
* `launch`: launches a coroutine
* `withContext`: returns a value
* `async/await`: return a value, later in time

A scope is the scope in the app which a coroutine can run. It is the lifecycle of the coroutine. It cancels all coroutines within the scope, when it finishes. It optimises resources.

One of the configuration options for a coroutine is a dispatcher. A dispatcher is a mechanism which changes wthread or thread pool the coroutine is executed on.

## Resources

### Tutorials
* [Android Data and Networking](https://www.raywenderlich.com/android/paths/androiddata) - Ray Wenderlich, March 2020 
* [Kotlin Coroutines: Fundamentals](https://www.raywenderlich.com/8458165-kotlin-coroutines-fundamentals) - Ray Wenderlich, 30 April 2020 
* [Kotlin Coroutines: In Depth](https://www.raywenderlich.com/5443783-kotlin-coroutines-in-depth) - Ray Wenderlich, 28 January 2020 
* [Kotlin Coroutines: Infographic](https://www.raywenderlich.com/8081891-kotlin-coroutines-infographic) - Ray Wenderlich, 8 July 2020
* [Retrofit and Coroutines](https://www.raywenderlich.com/10799127-retrofit-and-coroutines) - Ray Wenderlich, 1 September 2020 