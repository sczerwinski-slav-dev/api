> Annabeth gripped the hilt of her dagger. “A bounty on our heads…
> as if we didn’t attract enough monsters already.”
> 
> —Rick Riordan, “The Mark of Athena”

Dependency injection provided by [Dagger][dagger] is arguably one of the
greatest improvements to Android development. However, it still requires
a fair amount of boilerplate code to work. [Hilt][dagger:hilt] significantly
reduces the complexity of Dagger setup in an Android project, but it is
still far from the neat dependency injection provided by [Spring][spring].

In an attempt to make Hilt setup even more elegant, I have released an
[Android Hilt Extensions][sczerwinski:android-hilt] library. Part&nbsp;I
of the “Simplifying Room” series describes one of the most common use cases
that benefit from the proposed solutions.

## Basic Room Setup

Before we move on to the Hilt setup, let’s define the basic database
structure to work with. I will not go into great detail, as there are
already plenty of online resources covering this topic.

### Entities

First, we need to define some entities. Two should be enough to show
the idea.

```kotlin
@Entity(tableName = "users")
data class User(
    @PrimaryKey val id: Long,
    val username: String,
    @ColumnInfo(name = "display_name") val displayName: String
)

@Entity(
    tableName = "posts",
    foreignKeys = [
        ForeignKey(
            entity = User::class,
            parentColumns = "id",
            childColumns = "author_id",
            onDelete = ForeignKey.CASCADE
        )
    ]
)
data class Post(
    @PrimaryKey val id: Long,
    @ColumnInfo(name = "author_id") val authorId: Long,
    val title: String,
    val url: String
)
```

I will skip defining `@Relation`, as it’s not the subject of this article.

### DAOs

Having defined the entities, we can now create DAOs:
```kotlin
@Dao
interface UserDao {
    @Query("SELECT * FROM users")
    suspend fun getAll(): List<User>
}

@Dao
interface PostDao {
    @Query("SELECT * FROM posts WHERE author_id IS :authorId")
    suspend fun getByAuthor(authorId: Long): List<Post>
}
```

Again, both DAOs are extremely basic, just to set the background for the
database class.

### Database

Now, let’s define the database class:
```kotlin
@Database(
    entities = [
        User::class,
        Post::class
    ],
    version = 1
)
abstract class BlogDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
    abstract fun postDao(): PostDao
}
```

## The Old Way: Standard Hilt Module

In order to provide the database and the DAOs, we need to define
a Hilt module:
```kotlin
@Module
@InstallIn(SingletonComponent::class)
object BlogDatabaseModule {

    @Provides
    @Singleton
    fun provideBlogDatabase(
        @ApplicationContext context: Context
    ): BlogDatabase =
        Room.databaseBuilder(context, BlogDatabase::class.java, "blog.db")
            .build()

    @Provides
    @Singleton
    fun provideUserDao(database: BlogDatabase): UserDao =
        database.userDao()

    @Provides
    @Singleton
    fun providePostDao(database: BlogDatabase): PostDao =
        database.postDao()
}
```

However, I can see at least two problems with this approach:
1. The logic of factory methods providing the database and the DAOs
   is moved away from the database and into a completely separate
   object (i.e. the Dagger module), incentifying developers to group
   unrelated factory methods in a single module, rather than with
   the objects being created.
2. Some of the `@Provides` methods do nothing more but call another
   method from the instance of `BlogDatabase`.

## The New Way: Android Hilt Extensions

[Android Hilt Extensions][sczerwinski:android-hilt] provide a simpler
and more flexible alternative: `@FactoryMethod` annotation.

First, we need to add the following dependencies to the project:
```kotlin
dependencies {
    implementation("it.czerwinski.android.hilt:hilt-extensions:1.1.0")
    kapt("it.czerwinski.android.hilt:hilt-processor:1.1.0")
}
```

Now, instead of creating a separate Hilt module, we can define a `Factory`
object in our `BlogDatabase`, and annotate existing factory methods:
```kotlin
@Database(
    entities = [
        User::class,
        Post::class
    ],
    version = 1
)
abstract class BlogDatabase : RoomDatabase() {

    @FactoryMethod
    @Singleton
    abstract fun userDao(): UserDao

    @FactoryMethod
    @Singleton
    abstract fun postDao(): PostDao

    object Factory {

        @FactoryMethod
        @Singleton
        fun create(
            @ApplicationContext context: Context
        ): BlogDatabase =
            Room.databaseBuilder(context, BlogDatabase::class.java, "blog.db")
                .build()
    }
}
```

So… we no longer need to create any Hilt modules? Not really. Hilt
modules are still created (actually even more of them), but now, they are
all in the generated sources. The main application code is cleaner and
grouped by its functionality.

What do you think about `@FactoryMethod` annotation? Do you like how it
simplifies dependency injection or do you prefer the old way with
a separate module?

Feel free to leave your comment below or start a new
[discussion on GitHub][github:android-hilt:discussions].

If you’ve found a bug in the library or if you think it’s missing an
important feature, you’re welcome to create a
[new issue][github:android-hilt:issues].

**Next:** [Part II: Prepopulated Data And Migrations][simplifying-room-2]

---

## Credits

- “The Mark of Athena” quote comes from [Quotes.Pub][quote:riordan]
- [Image][pixabay:1729902]
  by [rihaij][pixabay:rihaij-2145:ref-1729902]
  from [Pixabay][pixabay:ref-1729902]


[simplifying-room-2]: https://slav.dev/posts/simplifying-room-2

[dagger]: https://dagger.dev/
[dagger:hilt]: https://dagger.dev/hilt/
[spring]: https://spring.io/

[sczerwinski:android-hilt]: https://github.com/sczerwinski/android-hilt

[github:android-hilt:discussions]: https://github.com/sczerwinski/android-hilt/discussions
[github:android-hilt:issues]: https://github.com/sczerwinski/android-hilt/issues/new/choose

[quote:riordan]: https://quotes.pub/q/annabeth-gripped-the-hilt-of-her-dagger-a-bounty-on-our-head-353304

[pixabay:1729902]: https://pixabay.com/photos/weapon-knife-blade-dagger-1729902/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1729902
[pixabay:rihaij-2145:ref-1729902]: https://pixabay.com/users/rihaij-2145/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1729902
[pixabay:ref-1729902]: https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1729902
