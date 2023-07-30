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

So each column of your table is assigned a version, that describes in which version if first appeared,
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

There are some special table classes, that add some additional functionality to your table.

#### 4.2.1. IdTable
An IdTable is a table, that has an `id` column, that has to be overridden by the user.

The type of the id must be specified as a generic type argument.

```kotlin
object UserTable : IdTable<Int>("users", 1) {
    override val id = column("id", IntColumnDesciption).primaryKey().autoIncrement()
}
```

### 4.2.2. IntIdTable
An IntIdTable is a [IdTable](#421-idtable), that has an `unsigned big int` as an id.

```kotlin
object UserTable : IntIdTable("users", 1) {
    // ...
}
```

### 4.2.3. UUIDIdTable
An UUIDIdTable is a [IdTable](#421-idtable), that has an `uuid` as an id.

The generation of the uuid can be more specified in the [Configuration](ConfigureAndConnect.md#31-configuration).
More on that in the final documentation. 

```kotlin
object UserTable : UUIDIdTable("users", 1) {
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

## 4.6. Relations
There are three relations, you can define in QuillORM, `oneToOne`, `oneToMany` and `manyToMany`.

All of these can just be defined by creating a column with an `refernce` on it, like this:

```kotlin
object UserSessionTable : Table("user_sessions", 1) {
    val id = column("id", IntColumnDesciption).primaryKey().autoIncrement().version(1)
    val userId = column("user_id", IntColumnDesciption).reference(UserTable.id).version(1)
}
```

However, this will only add a property with a type of `Int` to the model and will not load the referenced model.

### 4.6.1. oneToOne and oneToMany
To create a real reference, you should use the `belongsToOne` or `belongToMany` functions to create the reference.
In the model, this will create a property with the type of the referenced model and in addition to that property,
a function with the same name, that can be used to add, remove and filter the referenced model.

The table reference needs to be an `IdTable`.

```kotlin
object UserSessionTable : Table("user_sessions", 1) {
    val id = column("id", IntColumnDesciption).primaryKey().autoIncrement().version(1)
    val userId = belongsToMany(UserTable).version(1)
}
```

Both functions will create a column with a generated name (usually the name of the referenced table with an `_id` suffix,
e.g. `user_id` for `UserTable`) and the type of the id of the referenced table.

In the model a one-to-one relation will create a property with the type of the referenced model and a one-to-many
relation will create a property with the type of `List<ReferencedModel>`.

In the referenced model a reverse reference will be created, that can be used to get the model, that references the
current model.

### 4.6.2. manyToMany
To create a many-to-many relation, you can use the `manyToMany` function, that takes the referenced table as an argument.

```kotlin
object UserSessionTable : Table("user_sessions", 1) {
    val id = column("id", IntColumnDesciption).primaryKey().autoIncrement().version(1)
    val userId = manyToMany(UserTable).version(1)
}
```

This will create a seperated table with the name of the two referenced tables, sorted alphabetically, with an `_` in between,
e.g. `user_sessions` for `UserTable` and `SessionTable`.

In the model a property with the type of `List<ReferencedModel>` will be created in both models.

If a many-to-many relation is defined by two tables, that both reverence each other, a
`CircularReferenceException` will be thrown.

To add data to the intermediate table, a block can be passed to the `manyToMany` function, that will add the columns
to the intermediate table.

To modify the intermediate table in a patch, the `modifyIntermediate` function can be used, that takes a block, 
that can modify the intermediate table.

```kotlin
object UserSessionTable : Table("user_sessions", 1) {
    val id = column("id", IntColumnDesciption).primaryKey().autoIncrement().version(1)
    val userId = manyToMany(UserTable) { table -> table.column("created_at", TimestampColumnDescription) }.version(1)
        .patch(2) { table ->
            modifyIntermediate { table ->
                table.column("updated_at", TimestampColumnDescription)
            }
        }
}
```

[Back: Configure and connect](ConfigureAndConnect.md#33-disconnect) |
[Next: ORM](ORM.md#5-orm)