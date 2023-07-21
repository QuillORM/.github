# 5. ORM
The ORM is one of the main parts of the library.
It contains the logic for the generated models and the database queries.

_It isn't responsible for the generation of the models._

## 5.1. The model
A model always consists at least of
- a model interface,
- a model sql builder interface,
- a model companion object,
- a model implementation and
- a model sql builder implementation.

The model interface describes the model in all its structural details, 
without bothering the viewer of the interface with any implementation details.

The model sql builder should, but must not be, a subclass of the model interface. 
It provides helps for the building of sql queries.

The companion object implements the sql builder of the model, to get access to the sql builder.

The model implementation is the actual implementation of the model interface,
it should not be used directly, but only through the model interface.

The model sql builder implementation is the actual implementation of the model sql builder interface,
it should not be used directly, but only through the model sql builder interface.

## 5.1.1. The model interface
The model interface is an interface, that must always implement the `Model` interface.
In addition to that, it must have the properties of the table, that it represents.

Also, a model must always have an `id` property, that is used to identify the model in the database.

```kotlin
interface User : Model {
    var id: Int
    var username: String
}
```

The `Model` interface itself, is a marker interface, that is used to identify the model interfaces.
In addition, it provides some methods, that the developer can use, for example
- `flush` to flush changes to the database,
- `rollback` to rollback changes and use the values that where fetched from the database,
- `fetch` to fetch the new values from the database and
- `delete` to delete the model from the database.

## 5.1.2. The model sql builder interface
The model sql builder interface is an interface, that must always implement the `ModelSqlBuilder<T>` interface.
In addition to that, it must have functions to build a sql query to select the model from the database.

```kotlin
interface Use : Model {
    // ...

    interface SqlBuilder : ModelSqlBuilder<User> {

        fun whereId(id: Int): UserSqlBuilder

        fun whereId(operator: SqlOperator, id: Int): UserSqlBuilder

        fun whereUsername(username: String): UserSqlBuilder

        fun whereUsername(operator: SqlOperator, username: String): UserSqlBuilder

        fun sortById(): UserSqlBuilder

        fun sortById(direction: SqlDirection): UserSqlBuilder

        fun sortByUsername(): UserSqlBuilder

        fun sortByUsername(direction: SqlDirection): UserSqlBuilder

        // ...

    }
}
```

The `ModelSqlBuilder<T>` provides some generic functions, that can use the properties of table object to perform the query.

Also, the `ModelSqlBuilder<T>` provides some finishing functions, that finish the query and return the result, like
- `first` to get the first result that matches the query,
- `firstOrNull` to get the first result that matches the query or null,
- `firstOrCreate` to get the first result that matches the query or create a new one,
- `all` to get all results that match the query,
- `count` to get the number of results that match the query or
- `exists` to check if there are any results that match the query.

## 5.1.3. The model companion object
The model companion object is an object, that must always implement the `ModelCompanion<T, Builder>` interface.
The `Builder` type is the type of the models sql builder.

The companion object must in addition to that also implement the `ModelSqlBuilder<T>` interface,
that the developer can use to start the building of a sql query.

```kotlin
interface Use : Model {
    // ...

    companion object : ModelCompanion<User, SqlBuilder>, SqlBuilder {
        
        protected override fun createBuilder() = SqlBuilderImpl()
        
        override fun whereId(id: Int) = createBuilder().whereId(id)
        
        override fun whereId(operator: SqlOperator, id: Int) = createBuilder().whereId(operator, id)
        
        // ...

    }
    
    interface SqlBuilder : ModelSqlBuilder<User> {
        
        // ...

    }
}
```

The `ModelCompanion<T, Builder>` interface provides some generic functions, 
that can be used to crated a `SqlBuilder` and start the building of a sql query.

## 5.1.4. The model implementation
The model implementation is a class, that must always implement the interface of the model and
extend the `ModelImpl` class. 

The `ModelImpl` class will require the table object, from which the model was generated.

```kotlin
class UserImpl : ModelImpl(Table), User {

    override var id: Int by from(Table.id)

    override var username: String by from(Table.username)

}
```

The `ModelImpl` is the class, that does the heavy lifting for the model,
like the actual fetching of the data from the database.

## 5.1.5. The model sql builder implementation
The model sql builder implementation is a class, that must always implement the interface of the model sql builder and
extend the `ModelSqlBuilderImpl<T>` class.

```kotlin
class UserSqlBuilderImpl : ModelSqlBuilderImpl<User>(), User.SqlBuilder {

    override fun whereId(id: Int) = where(Table.id, id)

    override fun whereId(operator: SqlOperator, id: Int) = where(Table.id, operator, id)

    override fun whereUsername(username: String) = where(Table.username, username)

    override fun whereUsername(operator: SqlOperator, username: String) = where(Table.username, operator, username)

    override fun sortById() = orderBy(Table.id)

    override fun sortById(direction: SqlDirection) = orderBy(Table.id, direction)

    override fun sortByUsername() = orderBy(Table.username)

    override fun sortByUsername(direction: SqlDirection) = orderBy(Table.username, direction)

    // ...
    
}
```

The `ModelSqlBuilderImpl<T>` is the class, that does the heavy lifting for the building of the sql query,
and provides the functions like `first` or `all` to finish the query.

## 5.2. Relations
A relation that is defined in the table will not only generate a property in the model,
but also a function with the same name as the property, that returns an interface,
that can be used to manage the relation.

```kotlin
interface User : Model {
    // ...

    var id: Int
    var groups: MutableList<Group>

    fun groups(): BelongsToManyGroupsForUser
    
    // ...
    
}
```

```kotlin
interface Group : Model {
    // ...

    var id: Int
    var users: MutableList<User>

    fun users(): BelongsToHasManyUsersForGroup
    
    // ...
    
}
```

### 5.2.1. The relation interface
A relation interface always first holds the type of the relation, then the type of the model, this model relates to and
then the model interface itself.

The types are the following:
- `BelongsTo` for any relation, where this model holds the foreign key.
- `HasOne` for a one-to-one relation, where the other model holds the foreign key.
- `HasMany` for a one-to-many relation, where the other model holds the foreign key.
- `BelongsToMany` for a many-to-many relation, where an intermediate table holds the foreign keys.

The relation interface needs to implement a relation interface, that depends on the type of the relation.
In addition, it needs to implement the sql builder of the target model, if the relation is a relation,
where there can be multiple target models.

```kotlin
interface BelongsToManyGroupsForUser : BelongsToMany<User, Group>, Group.SqlBuilder
```

To add or remove models from the relation, the relation interface provides the following functions:
- `attach` to attach a model to the relation,
- `detach` to detach a model from the relation and
- `sync` to sync the models of the relation.

In addition, the `MutableList` of the relation can be used to add or remove models from the relation. 

### 5.2.2. Many-to-many relations
For a many-to-many relation, the property will hold `Pair`s of the model and the intermediate data,
which is a model without a query builder.

On the relations interface, there will be a functions to filter on the intermediate data, for example
`wherePivotCreatedAt` to filter on the `createdAt` column of the intermediate data.

```kotlin
interface User : Model {
    // ...

    var id: Int
    var groups: MutableList<Pair<Group, UserGroup>>

    fun groups(): BelongsToManyGroupsForUser
    
    // ...
    
}
```

[Back: Table structure](TableStructure.md#462-manytomany) | 
[Next: Model generation](ModelGeneration.md#6-model-generation)