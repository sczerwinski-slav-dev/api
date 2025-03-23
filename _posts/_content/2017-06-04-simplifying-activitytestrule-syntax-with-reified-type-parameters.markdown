## Introduction

In this article, I am using JUnit&nbsp;4.12,
and [Espresso](https://google.github.io/android-testing-support-library/docs/espresso/index.html)&nbsp;2.2.2
with [Espresso Intents](https://google.github.io/android-testing-support-library/docs/espresso/intents/index.html).

Since Espresso uses old version of Android Support Annotations (i.e.&nbsp;23.1.1),
it is mandatory to exclude this module when using latest Android Support library:

```gradle
dependencies {
    // […]
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    androidTestCompile('com.android.support.test.espresso:espresso-intents:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
}
```

## The Problems

When [testing Android UI](https://developer.android.com/training/testing/ui-testing/espresso-testing.html)
with Espresso, I need to define an `ActivityTestRule` or an `IntentsTestRule`
(in the examples, I will use the latter). Often, I wish to start the `Activity`
under test with a specific `Intent`, so the rule must be instantiated by calling
a constructor with three arguments:

```kotlin
@RunWith(AndroidJUnit4::class)
class MyTestClass {

    @Rule
    @JvmField
    val mainActivityRule = IntentsTestRule(
            MainActivity::class.java, false, false)
}
```

* The first argument is the class of the activity.
* The second one regards initial touch mode (it is `false` by default,
  and usually, I don’t need to change it).
* And the last argument tells whether the activity should be launched
  automatically before each test method
  (by default it is set to `true`).

There are several problems with this snippet of code:

1. When I see the constructor call, I don’t know the meaning of each `false`
  argument without looking at the definition of the constructor. In Kotlin,
  I could use [named arguments](https://kotlinlang.org/docs/reference/functions.html#named-arguments),
  e.g. `launchActivity = false`, but it is not allowed with non-Kotlin functions.
2. I have to provide `initialTouchMode`, even though I don’t need to change
  its default value. The second argument is only there to prevent ambiguity,
  because a constructor:
  `ActivityTestRule(Class<T> activityClass, boolean launchActivity)`
  would have the same arguments as the already existing one:
  `ActivityTestRule(Class<T> activityClass, boolean initialTouchMode)`.
3. The `::class.java` after the activity class makes the code obscure.

All these problems could be easily avoided in Kotlin, but the Espresso library
is written in Java.

## Using Kotlin Functions

To solve the first and the second issue, I created a simple Kotlin function with
[default argument values](https://kotlinlang.org/docs/reference/functions.html#default-arguments):

```kotlin
fun <T : Activity> intentsTestRule(
        activityClass: Class<T>,
        initialTouchMode: Boolean = false,
        launchActivity: Boolean = true) =
    IntentsTestRule(activityClass, initialTouchMode, launchActivity)
```

As a result, I can name the third argument and omit the second one:

```kotlin
@RunWith(AndroidJUnit4::class)
class MyTestClass {

    @Rule
    @JvmField
    val mainActivityRule = intentsTestRule(
            MainActivity::class.java,
            launchActivity = false)
}
```

## Using Reified Type Parameters

To solve the last problem, I needed to harness Kotlin
[reified type parameters](https://kotlinlang.org/docs/reference/inline-functions.html#reified-type-parameters).

After reifying parameter&nbsp;`T`, I can directly access its type inside
the function (note that I can only do that with `inline` functions). Thus,
the first argument is no longer needed:

```kotlin
inline fun <reified T : Activity> intentsTestRule(
        initialTouchMode: Boolean = false,
        launchActivity: Boolean = true) =
    IntentsTestRule(T::class.java, initialTouchMode, launchActivity)
```

So instead of providing the argument, I can just define the type parameter:

```kotlin
@RunWith(AndroidJUnit4::class)
class MyTestClass {

    @Rule
    @JvmField
    val mainActivityRule = intentsTestRule<MainActivity>(launchActivity = false)
}
```

A similar function might be defined for `ActivityTestRule`s.

---

Full implementation with examples is available [here](https://gist.github.com/sczerwinski/bcd7e38a02638c249878b78b2e9e6cd0).

## Bibliography

* [_Functions_, Kotlin Programming Language Reference](https://kotlinlang.org/docs/reference/functions.html)
* [_Inline Functions_, Kotlin Programming Language Reference](https://kotlinlang.org/docs/reference/inline-functions.html)
* [Espresso Documentation](https://google.github.io/android-testing-support-library/docs/espresso/index.html)
* [_Testing UI for a Single App_, Android Developers](https://developer.android.com/training/testing/ui-testing/espresso-testing.html)

---

## Credits

- [Image][pixabay:1234649]
  by [günter][pixabay:moritz320-1260270:ref-1234649]
  from [Pixabay][pixabay:ref-1234649]


[pixabay:1234649]: https://pixabay.com/photos/drunk-espresso-espresso-cup-coffee-1234649/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1234649
[pixabay:moritz320-1260270:ref-1234649]: https://pixabay.com/users/moritz320-1260270/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1234649
[pixabay:ref-1234649]: https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1234649
