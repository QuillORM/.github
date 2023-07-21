# 4. Table Structure
To understand the key concept of QuillORM, you need to understand our vision first,
let the developer focus on the structure of the application and not the writing of
_boilerplate like_ models.

To be honest, Models are a little bit more than just boilerplate, but they are most likely an
exact copy of you database structure.

**But why write it a second time?**

That is the main part of QuillORM, you do not need to write that twice.
Just write your table structure once and QuillORM will generate the models for you.

## 4.1. Versioning
The structure of your database is not static, as development progresses,
the structure will change. For this reason, QuillORM provides a way to version your database structure.

So each field of your table is assigned a version, that describes in which version if first appeared,
afterward you can patch that definition in later versions to modify your structure.

In addition to that, each table has a target version, that describes, in which version the table should be.
QuillORM will automatically migrate that table to the target version, if it is not already.

For this a seperated table `__quill_version` is created, that stores the current version of each table,
please do not mess with that table, as it is used by QuillORM.

Also, your table might have some data in it, when transforming the structure. QuillORM tries to resolve most of
the problems, but some problems can not be solved automatically. For this you can provide `transform` and `revert`
block, that describe how the data is transformed from one version to another.

## 4.2. The table object
The table object is the main object, that describes the structure of your table.

It is defined by an object, that extends the `Table` class (or any subclass of it)
and has a `table` and `targetVersion` property.

```kotlin
object UserTable : Table("users", 1) {
    // ...
}
```

## 4.3. The table structure
To define the structure of your table, you simply need to add a property to your table object,
that uses the `column` function to define the column.

The `column` function needs in addition to the name of the column and `ColumnDescription` object,
that defines, what type the column has.

You can also use some other function, that will later be described in the documentation,
that use that `column` function, such as `int` or `varchar`.

```kotlin
object UserTable : Table("users", 1) {
    val id = column("id", IntColumnDesciption)
    val name = column("name", VarcharColumnDescription(255))
}
```

The `column` function returns an `Column<T>` instance, that describes the column.

On that instance, other function can be called, that describe the column further,
such as `primaryKey` or `autoIncrement`.

```kotlin
object UserTable : Table("users", 1) {
    val id = column("id", IntColumnDesciption).primaryKey().autoIncrement()
    val username = column("username", VarcharColumnDescription(255)).unique()
}
```

## 4.4. Column versioning
To add a version to the column, you can use the `version` function, that takes the version as an argument.

```kotlin
object UserTable : Table("users", 1) {
    val id = column("id", IntColumnDesciption).primaryKey().autoIncrement().version(1)
    val username = column("username", VarcharColumnDescription(255)).unique().version(1)
}
```
This would describe both columns to be added in version 1.

To apply a patch in any version after that, you can use the `patch` function, that takes the version and
a block, that describes the patch as arguments.

```kotlin
object UserTable : Table("users", 1) {
    val id = column("id", IntColumnDesciption).primaryKey().autoIncrement().version(1)
    val username = column("username", VarcharColumnDescription(255)).unique().version(1)
        .patch(2) { removeUnique().nullable() }
}
```

If no version is defined for that column and the table is used, a `ColumnVersionException` will be thrown.

## 4.5. Transforming data
To transform the data of a table from one version to another, the `transform` and `revert` functions can be used.

The `transform` function takes a `toVersion` and `fromVersion`, that describe the versions on which the transformation
should be applied. In addition, a block is passed, that takes the `SoruceRow` and an `TargetRow` as an argument 
that can than be changes.

QuillORM tries to transform as much data as possible automatically, but some data can not be transformed automatically,
some automatic transformations are type conversions (e.g. `Int` to `Long`), but also some other transformations, such as
`String` to `UUID` or `String` to `LocalDate`.

If QuillORM can not transform the data automatically, it may throw a `DataTransformationException`,
depending on the specific problem.

```kotlin
object UserTable : Table("users", 1) {
    val id = column("id", IntColumnDesciption).primaryKey().autoIncrement().version(1)
    val username = column("username", VarcharColumnDescription(255)).unique().version(1)
        .patch(2) { removeUnique().nullable() }
    
    init {
        revert(fromVersion = 2) { source, target ->
            target[username] = source[username].toString()
        }
    }
    
}
```

[Back: Configure and connect](ConfigureAndConnect.md#33-disconnect) |
[Next: ORM](ORM.md#5-orm)