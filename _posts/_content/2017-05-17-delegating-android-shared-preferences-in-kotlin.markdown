> I have learned to delegate.
> 
> —Gwen Stefani

## The Problem: Boilerplate Code

[`SharedPreferences`][android:SharedPreferences]
provide a relatively simple way of storing key-value pairs on Android.
The application might not only use them to save settings, but also to
keep its persistent state.

However, when the app grows more complex, using `SharedPreferences` might
become painful. The boilerplate code required to store and retrieve
a preference takes more lines than the actual operation performed on its value.

For example, consider a `LoginActivity` that only displays input fields
for username and password. As a user, I want the application to remember
my username so that I don’t need to type it again the next time I want
to log in—true _story_ (pun intended).

How can this be done in Java?

First, we need to obtain an instance of `SharedPreferences` object.
Assuming our code is in the `Activity`, we can do it by calling
`getSharedPreferences()`:

```java
SharedPreferences sharedPreferences =
        getSharedPreferences(
            "it.czerwinski.myapp.MY_PREFERENCES",
            Context.MODE_PRIVATE);
```

Or, if multiple preferences files are not needed, we may use the default
shared preferences instead:

```java
SharedPreferences sharedPreferences =
        PreferenceManager.getDefaultSharedPreferences(this);
```

We may then retrieve a `String` by calling the `getString()` method, which
requires two arguments: the name of the preference, and the default value,
returned if the preference does not exist:

```java
usernameEditText.setText(
        sharedPreferences.getString("USERNAME", ""));
```

Storing a value, however, is not that straightforward. `SharedPreferences`
object only provides methods to get preferences. To modify their values,
we need to use an `Editor`, returned by the `edit()` method:

```java
sharedPreferences.edit()
        .putString("USERNAME", usernameEditText.getText().toString())
        .apply();
```

We shouldn’t forget to call `apply()`. Otherwise, the value would not be saved.

This code looks simple and clean… as long as we only need two or three calls
to `SharedPreferences`. But typically, that is not the case, is it?

## Anko Comes To The Rescue

Kotlin language is probably the best thing that has happened to Android
developers since first Android phones hit the market. It allowed creation
of powerful yet lightweight tools that simplify our lives.

One of those tools, Anko, provides an
[extension property][kotlin:extension_properties]
of the `Context` class, called `defaultSharedPreferences`, that eliminates
the need to explicitly initialize the `SharedPreferences` object.
Nonetheless, we still need to retrieve and save the values in the same
inconvenient manner:

```kotlin
usernameEditText.setText(
        defaultSharedPreferences.getString("USERNAME", ""))
```

```kotlin
defaultSharedPreferences.edit()
        .putString("USERNAME", usernameEditText.text.toString())
        .apply()
```

## Utilize Kotlin Properties

If the same statements occur in code more than once, they should be extracted
into a separate method, or in this case, two methods—`getUsername` and
`setUsername`.

But since we are using Kotlin, why not declare a property with defined custom
[getter and setter][kotlin:properties:get_set]?
We can also make it an extension property, so it might be referred from any
`Activity` in our app:

```kotlin
var Context.username: String
    get() = defaultSharedPreferences.getString("USERNAME", "")
    set(value) = defaultSharedPreferences.edit()
            .putString("USERNAME", value)
            .apply()
```

As a result, the calls to save and restore the username become much cleaner:

```kotlin
usernameEditText.setText(username)
```

```kotlin
username = usernameEditText.text.toString()
```

However simple, this approach still has its drawbacks. The declaration of the
`username` property, even though a bit complicated, at least keeps
`SharedPreferences` in one place. But imagine that there are 10&nbsp;similar
properties in the app… or&nbsp;20… or even&nbsp;50…

## Delegate

> There are certain common kinds of properties, that, though we can implement
> them manually every time we need them, would be very nice to implement once
> and for all […]
> 
> —Delegated Properties, Kotlin Programming Language Reference

The above quotation perfectly conveys our case. And Kotlin offers us
a solution—[delegated properties][kotlin:delegated_properties].

So let’s create a delegate that handles values stored in `SharedPreferences`:

```kotlin
class StringSharedPreferenceDelegate(
        context: Context,
        private val key: String,
        private val defaultValue: String) {

    private val sharedPreferences by lazy {
        PreferenceManager.getDefaultSharedPreferences(context)
    }

    operator fun getValue(ref: Any?, property: KProperty<*>): String =
            sharedPreferences.getString(key, defaultValue)

    operator fun setValue(ref: Any?, property: KProperty<*>, value: String): Unit {
        sharedPreferences.edit()
                .putString(key, value)
                .apply()
    }
}
```

Now, the property can be delegated to an instance of
`StringSharedPreferenceDelegate` in a single line of code:

```kotlin
var username: String by StringSharedPreferenceDelegate(context, "USERNAME", "")
```

## Reuse

> […] and put into a library.
> 
> —Delegated Properties, Kotlin Programming Language Reference

Probably most Android applications use `SharedPreferences` at some point.
So I decided to implement generalized version of delegates and put them into
a reusable [library][github:android-delegates-shared-preferences].

To use the delegates simply add a dependency to your `build.gradle` (the package
is available in [_jcenter_][jcenter:delegates-shared-preferences]
repository):

```gradle
dependencies {
    compile 'it.czerwinski.android:delegates-shared-preferences:0.1'
}
```

A delegated property can then be defined in your `Activity` class:

```kotlin
var username by stringSharedPreference("USERNAME", "")
```

or in any other class containing a reference to the `Context`:

```kotlin
var username by context.stringSharedPreference("USERNAME", "")
```

It might be a good idea to define an additional
[extension function][kotlin:extension_function],
at least for those preferences, which are used in many different classes:

```kotlin
fun Context.usernameSharedPreference() =
        stringSharedPreference("USERNAME", "")
```

```kotlin
var username by usernameSharedPreference()
```

## What’s Next?

So far, the library only supports several basic data types:
`Int`, `Long`, `Float`, `Double`, `Boolean`, `String` and `Set<String>`.
But in the future, I plan to implement delegates for other common types,
e.g. `Array`, `List`, `Date`, as well as `data` classes.

In the meantime, feel free to use the existing features.
The library is still in development stage, but it has been fully tested.

---

## Credits

- Gwen Stefani quote comes from [BrainyQuote][quote:delegate]
- Image by [Gerd Altmann][pixabay:geralt-9301]
  from [Pixabay][pixabay:1971162]


[android:SharedPreferences]: https://developer.android.com/reference/android/content/SharedPreferences.html

[kotlin:properties:get_set]: https://kotlinlang.org/docs/reference/properties.html#getters-and-setters
[kotlin:delegated_properties]: https://kotlinlang.org/docs/reference/delegated-properties.html
[kotlin:extension_properties]: https://kotlinlang.org/docs/reference/extensions.html#extension-properties
[kotlin:extension_function]: https://kotlinlang.org/docs/reference/extensions.html#extension-functions

[github:android-delegates-shared-preferences]: https://github.com/sczerwinski/android-delegates-shared-preferences/tree/master

[jcenter:delegates-shared-preferences]: https://bintray.com/sczerwinski/android/delegates-shared-preferences

[quote:delegate]: https://www.brainyquote.com/quotes/quotes/g/gwenstefan468230.html

[pixabay:geralt-9301]: https://pixabay.com/users/geralt-9301/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1971162
[pixabay:1971162]: https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1971162
