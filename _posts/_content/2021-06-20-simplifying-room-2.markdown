> One thing is undeniable. If we are going to continue to have
> support for migration, we need to be able to control the numbers.
> 
> —Michael Gove

**Previous:** [Part I: Hilt Setup][simplifying-room-1]

SQLite databases were always a part of Android. However, without an
object–relational mapping framework like [Hibernate][hibernate], they
were extremely tedious to handle. Since [Room][room] came along, almost
all aspects of database management have been significantly simplified…
with two exceptions: prepopulating and migrations.

In my [Android Room Extensions][sczerwinski:android-room] library,
I have included a set of utility functions solving these problems.

Part&nbsp;II of the “Simplifying Room” series describes how to add
prepopulated data and migrations to the database defined in
[Part&nbsp;I][simplifying-room-1].

## The Old Way

### Prepopulating: Create Database From File

[Android Developers website][room:prepopulate] recommends using
`createFromAsset()` and `createFromFile()` to create a prepopulated
database from a prepackaged file:
```kotlin
@FactoryMethod
@Singleton
fun create(
    @ApplicationContext context: Context
): BlogDatabase =
    Room.databaseBuilder(context, BlogDatabase::class.java, "blog.db")
        .createFromFile(initialDatabaseFile)
        .build()
```

The problem with this approach is that the initial data must already be
stored in an SQLite database embedded in the app. As a binary file, the
prepopulated database is not convenient to update and impossible to track
changes.

### Prepopulating: Database Callback

In order to define the initial data SQL queries, one can implement
a `RoomDatabase.Callback`, and execute the SQL in `onCreate()` method:
```kotlin
class PopulateDatabaseCallback : RoomDatabase.Callback() {

    override fun onCreate(db: SupportSQLiteDatabase) {
        // Execute SQL statements here
    }
}
```

The callback can be added to the database builder:
```kotlin
@FactoryMethod
@Singleton
fun create(
    @ApplicationContext context: Context
): BlogDatabase =
    Room.databaseBuilder(context, BlogDatabase::class.java, "blog.db")
        .addCallback(PopulateDatabaseCallback())
        .build()
```

However, all the SQL statements are defined as string literals
in the source code of the application.

### Migrations

Similarly, when a manual database migration is necessary,
the SQL statements could be executed by a concrete `Migration`
(as recommended on [Android Developers website][room:migrate]):

```kotlin
val MIGRATION_1_2 = object : Migration(1, 2) {

    override fun migrate(database: SupportSQLiteDatabase) {
        // Execute SQL statements here
    }
}
```

```kotlin
@FactoryMethod
@Singleton
fun create(
    @ApplicationContext context: Context
): BlogDatabase =
    Room.databaseBuilder(context, BlogDatabase::class.java, "blog.db")
        .addMigrations(MIGRATION_1_2)
        .build()
```

## The New Way: Android Room Extensions

With [Android Room Extensions][sczerwinski:android-room], it is possible
to define SQL scripts for prepopulated data and migrations in separate
files or assets.

First, we need to add the following dependencies to the project:
```kotlin
dependencies {
    implementation("it.czerwinski.android.room:room-extensions:1.0.1")
}
```

Now, the SQL scripts can be defined as application Assets, for example:
```
assets/
  sql/
    prepopulate.sql
    migrate_1_2.sql
    migrate_2_3.sql
    …
```

To use those scripts in the app, we just need to call
`populateFromSqlAsset()` and `addMigrationsFromSqlAssets()`
on the database builder:
```kotlin
@FactoryMethod
@Singleton
fun create(
    @ApplicationContext context: Context
): BlogDatabase =
    context.roomDatabaseBuilder<BlogDatabase>()
        .populateFromSqlAsset(context, "sql/prepopulate.sql")
        .addMigrationsFromSqlAssets(
            context,
            sqlFilePathFormat = "sql/migrate_{}_{}.sql"
        )
        .build()
```

_Note that instead of `Room.databaseBuilder()`, we are using a more
convenient `roomDatabaseBuilder()` extension function._

What do you think about these Android Room Extensions? Do you like the
idea of having SQL scripts located in separate assets, or would you rather
execute SQL queries using `SupportSQLiteDatabase`?

Feel free to leave your comment below or start a new
[discussion on GitHub][github:android-room:discussions].

If you’ve found a bug in the library or if you think it’s missing an
important feature, you’re welcome to create a
[new issue][github:android-room:issues].

---

## Credits

- Michael Gove quote comes from [BrainyQuote][quote:migration]


[simplifying-room-1]: https://slav.dev/posts/simplifying-room-1

[room]: https://developer.android.com/training/data-storage/room
[room:prepopulate]: https://developer.android.com/training/data-storage/room/prepopulate
[room:migrate]: https://developer.android.com/training/data-storage/room/migrating-db-versions
[hibernate]: https://hibernate.org/

[sczerwinski:android-room]: https://github.com/sczerwinski/android-room

[github:android-room:discussions]: https://github.com/sczerwinski/android-room/discussions
[github:android-room:issues]: https://github.com/sczerwinski/android-room/issues/new/choose

[Pixabay:Georg_Wietschorke]: https://pixabay.com/users/georg_wietschorke-3238642/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1773758
[Pixabay:migration]: https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1773758

[quote:migration]: https://www.brainyquote.com/quotes/michael_gove_760072
