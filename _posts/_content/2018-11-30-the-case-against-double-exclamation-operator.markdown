> The third option is for NPE-lovers: the not-null assertion
> operator (`!!`)
> 
> —[Kotlin reference](https://kotlinlang.org/docs/reference/null-safety.html#the--operator)

The _not-null assertion operator_ (a.k.a. double exclamation operator)
converts a value of nullable type to a non-null type, or throws
a `KotlinNullPointerException` if the converted value is `null`.
If such an operator exists in Kotlin, it can be used in the code.
But just because you can, doesn't mean you should. In this article,
I will argue that using not-null assertion operator should be avoided.

## Make use of Kotlin non-null types

Let's consider the following class as an example:

```kotlin
class ButtonsPanel(private val ctx: Context?) : LinearLayout(ctx) {

    fun addButton(@StringRes resId: Int?) {
        val caption = ctx!!.getString(resId!!)
        val button = createButton(caption)
        addView(button)
    }

    private fun createButton(caption: String): Button = …
}
```

We can add more buttons to the `ButtonsPanel` by calling
the method `addButton()` with string resource ID as an argument.
If, however, either the `ctx` or the `resId` is `null`, the method
will throw a `KotlinNullPointerException`, causing the app to crash.

But why was the `!!` operator used at all?

One possibility is that neither `ctx` nor `resId` should ever be `null`,
in which case both should be defined with non-null types:

```kotlin
class ButtonsPanel(private val ctx: Context) : LinearLayout(ctx) {

    fun addButton(@StringRes resId: Int) {
        val caption = ctx.getString(resId)
        val button = createButton(caption)
        addView(button)
    }

    private fun createButton(caption: String): Button = …
}
```

Yet, if the usage of `null` values is intentionally allowed, these cases
should be properly handled in the method:

```kotlin
class ButtonsPanel(private val ctx: Context?) : LinearLayout(ctx) {

    fun addButton(@StringRes resId: Int?) {
        val caption = resId?.let { ctx?.getString(it) }.orEmpty()
        val button = createButton(caption)
        addView(button)
    }

    private fun createButton(caption: String): Button = …
}
```

Naturally, cases in which `ctx` is non-null and `resId` is nullable
(or vice versa) are still possible.

## Using 3rd party Java libraries

Sometimes we don't have control over the interface we're implementing.
When using a Java library, we may need to implement API with no defined
nullability, i.e. fields or method parameters annotated with neither
`@NonNull` nor `@Nullable`:

```java
abstract public class AbstractButtonPanel extends LinearLayout {

    protected Context ctx;

    public AbstractButtonPanel(Context context) {
        super(context);
        this.ctx = context;
    }

    abstract public void addButton(@StringRes Integer resId);
}
```

To stay on the safe side, we should always assume that both `ctx`
and `resId` are nullable:

```kotlin
class ButtonsPanel(ctx: Context?) : LinearLayout(ctx) {

    override fun addButton(@StringRes resId: Int?) {
        val caption = resId?.let { ctx?.getString(it) }.orEmpty()
        val button = createButton(caption)
        addView(button)
    }

    private fun createButton(caption: String): Button = …
}
```

But what can we do if we need to enforce non-null values?

### Require non-null arguments

> `IllegalArgumentException` – Thrown to indicate that a method has been passed
> an illegal or inappropriate argument.
> 
> —[Java Platform Documentation](https://docs.oracle.com/javase/7/docs/api/java/lang/IllegalArgumentException.html)

When we pass an argument value compatible with the type of the function
parameter, but invalid in any different way, we will typically expect
an [`IllegalArgumentException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-argument-exception/index.html).
To validate value of an argument in Kotlin, we can use various
`require*()` functions, in this case
[`requireNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/require-not-null.html):

```kotlin
override fun addButton(@StringRes resId: Int?) {
    val nonNullResId = requireNotNull(resId) {
        "Resource ID cannot be null"
    }
    val caption = ctx?.getString(nonNullResId).orEmpty()
    val button = createButton(caption)
    addView(button)
}
```

### Check state of the fields

> `IllegalStateException` – Signals that a method has been invoked at an illegal
> or inappropriate time. In other words, the Java environment or Java application
> is not in an appropriate state for the requested operation.
> 
> —[Java Platform Documentation](https://docs.oracle.com/javase/7/docs/api/java/lang/IllegalStateException.html)

Similarly, if the state of the class is not valid for an operation,
(i.e. any of the fields contains a value that prevents the operation
from being performed), the method is supposed to throw
an [`IllegalStateException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-illegal-state-exception/index.html).
Kotlin provides `check*()` functions for this purpose, in particular
[`checkNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/check-not-null.html):

```kotlin
override fun addButton(@StringRes resId: Int?) {
    val nonNullResId = requireNotNull(resId) {
        "Resource ID cannot be null"
    }
    val nonNullContext = checkNotNull(ctx) {
        "Cannot add a button without a context"
    }
    val caption = nonNullContext.getString(nonNullResId)
    val button = createButton(caption)
    addView(button)
}
```

## So how is it better than NPE?

Unlike `!!` operator, `requireNotNull()` and `checkNotNull()` functions
provide specific exceptions and allow defining custom error messages.
This allows more meaningful logs when the application crashes.

Moreover, double exclamation operator is easy to miss in the code,
or to confuse with null-safe question mark, making debugging process
even more tedious.

Still, it is best to consider null safe operators `?.` and `?:`
in each case, instead of throwing exceptions.
