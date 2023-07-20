# 4. Evolutions
Evolutions are the key concept of QuillORM, that manage to take as many work as possible from the developer.

To see a brief overview of the concept, 
see [1.3. The key concept: Evolutions only](Intoduction.md#13-the-key-concept-evolutions-only).

## 4.1. Why evolutions?
The first question, we asked ourselves when creating QuillORM was: "How can we create a system, that does not require
the developer to write the same thing twice?".

The answer to that question are evolutions.

Evolutions are the only thing, that the developer has to write, to describe the database structure.

## 4.2. Basics
### 4.2.1. What are evolutions?
You can think of an evolution as a change in your database structure,
the next step in the evolution of your database, if you will.

Evolutions can be applied to your database and reverted from you database,
in addition they can map existing data to a new structure and revert that mapping.

### 4.2.2. How do evolutions work?
Evolutions are generally only applied once to your database, but they can be reverted and reapplied.

For this a seperated table `quill_evolutions` is created, that stores the state of the evolutions for your database.
You, as a developer, should not touch this table, as it is managed by QuillORM.

You as a developer only have to [write the evolutions](#43-writing-evolutions) and ensure, that the code that defines
the evolutions is executed, when your application starts.

Everything from that onwards is managed by QuillORM.

QuillORM will first order the evolutions, based on
1. their order (that can be defined by the developer) and
2. their name.

Then QuillORM will run through all evolutions and check, if they are already applied to all databases, they should be
applied to. If that is the case, the evolution is skipped, otherwise it is applied on that database and
the evolution is marked as applied in the `quill_evolutions` table.

## 4.3. Writing evolutions
### 4.3.1. Basics
An evolution must always have at least
- a `name`,
- a `table` it should be applied to,
- a `execute` function, that applies the evolution and
- an `undo` function, that reverts the evolution.

In addition, an evolution can have
- a `order` property, that defines the order of the evolution,
- a `transform` function, that maps existing data to a new structure and
- a `revert` function, that reverts the mapping.

### 4.3.2. Your first evolution
To start creating an evolution, the `Evolution` object needs to be invoked.
That invocation has a block with an `EvolutionStep` as its receiver.

On that receiver every property of the evolution can be set.

```kotlin
Evolution { // this: EvolutionStep
    this.name = "create user table"
    this.table = "users"
    
    execute { // this: EvolutionExecutionStep
        // TODO: create table
    }
    
    undo { // this: EvolutionExecutionStep
        // TODO: drop table
    }
}
```

So this is the minimal evolution, that can be created.

### 4.3.3. Ordering evolutions
As mentioned in [4.2.2. How do evolutions work?](#422-how-do-evolutions-work), evolutions are ordered by their order
and their name.

To set the order of an evolution, the `order` property can be set.

```kotlin
Evolution {
    this.name = "create user table"
    this.table = "users"
    this.order = 1
    
    /* ... */
}
```

### 4.3.4. Execute and undo
In the `execute` and `undo` block, the evolution can apply changes to the table it is applied to.
If the table does not exist yet, it will be created.

For this a `EvolutionExecutionStep` class is passed to the `execute` block.

So basically, the `exeucte` and `undo` block support the same operations.

It has functions for different types of changes, that can be applied to the table.

The `EvolutionExecutionStep` has some basic functions, that all other functions will and should rely on.
These functions are:
- `createTable`, that creates the table, if it already exists, an exception is thrown.
- `createTableIfNotExists`, that creates the table, if it does not exist yet. 
(The default action for a table in an evolution, if not defined otherwise)
- `dropTable`, that drops the table, if it exists, otherwise an exception is thrown.
- `dropTableIfExists`, that drops the table, if it exists, otherwise nothing happens.
- `renameTable`, that renames the table, if it exists, otherwise an exception is thrown.
- `renameTableIfExists`, that renames the table, if it exists, otherwise nothing happens.
- `addColumn`, that adds a column to the table, if it already exists, an exception is thrown.
This function requires an `name` and `type` for that column. The `type` is not n usual class, but a
`ColumnType` object, that defines how to map values between a `ResultRow` and the specific type.
- `renameColumn`, that renames a column, if it exists, otherwise an exception is thrown.
- `dropColumn`, that drops a column, if it exists, otherwise an exception is thrown.
- `addIndex`, that adds an index to the table, if it already exists, an exception is thrown.
- `renameIndex`, that renames an index, if it exists, otherwise an exception is thrown.
- `dropIndex`, that drops an index, if it exists, otherwise an exception is thrown.
- `addConstraint`, that adds a constraint to the table, if it already exists, an exception is thrown.
- `renameConstraint`, that renames a constraint, if it exists, otherwise an exception is thrown.
- `dropConstraint`, that drops a constraint, if it exists, otherwise an exception is thrown.

In addition, there are some functions, that base on that functions, but are more specific, like
- `varchar`, that adds a `VARCHAR` column to the table.
- `integer`, that adds an `INTEGER` column to the table.
- `primaryKey`, that adds a `PRIMARY KEY` constraint to the table.
- `foreignKey`, that adds a `FOREIGN KEY` constraint to the table.

and so on.

With the returned `Column` from the `addColumn` function, the column can be further configured. This will
be different depending on the type of the column. Some basic functions, that always exist are:
- `after`, that defines the column, that this column should be placed after.
- `autoIncrement`, that defines, if the column should be auto incremented.
A block can be provided, that can configure the auto increment further.
- `charset`, that defines the charset of the column.
- `collate`, that defines collate of the column.
- `comment`, that defines the comment of the column.
- `default`, that defines the default value of the column.
- `clientDefault` that defines the default value of the column on the client side.
- `first`, that defines, that this column should be the first column in the table.
- `generatedAs` that defines, that this column should be generated as a specific expression.
- `index`, that defines, that this column should be indexed.
- `nullable`, that defines, that this column should be nullable.
- `primary`, that defines, that this column should be the primary key.
- `unique`, that defines, that this column should be unique.
- `useCurrent`, that defines, that this column should use the current timestamp as default value.
- `useCurrentOnUpdate`, that defines, that this column should use the current timestamp as default value on update.

These are not all functions, but the most important ones.

The same goes for the index and contains, they will have functions, that are specific to them.

### 4.3.5. Relations
A relation between to tables can be defined in two ways:
1. By defining a foreign key directly on the table. This is however not the preferred way, as the column
will result in an `Column<Int>`, which makes all the necessary mapping to the related table task of the
developer.
2. By defining a relation to an existing model. This is the preferred way, as it will result in a
`Column<Entity<T>>`, which will handle all the mapping to the related table. However, pleas notice, that
you need to have generated the model for that other table, before you can use it.

In addition, the second way will also make a reverse relation available on the other table, which can help
retrieving the related entities.

```kotlin
Evolution {
    this.name = "create sessions table"
    this.table = "sessions"
    this.order = 2
    
    execute {
        integer("user_id").foreignKey("users", "id") {
            this.onUpdate = ForeignKeyAction.CASCADE
            this.onDelete = ForeignKeyAction.CASCADE
        }
        
        // Or, better:
        refernce(User) { // This is the User model from the previous example
            this.onUpdate = ForeignKeyAction.CASCADE
            this.onDelete = ForeignKeyAction.CASCADE
        }
    }
    
    undo {
        dropTableIfExists()
    }
}
```

### 4.3.6. Transform and revert
In addition to the `execute` and `undo` block, there is also a `transform` and `revert` block.

These blocks transform the existing data in the table, to the new structure, that is defined in the `execute`
block.

The `transform` block is executed after the `execute` block, and the `revert` block is executed before the
`undo` block.

The `transform` and `revert` block are optional, and can be omitted.

They need to provide a block with, that receives a `EvolutionTransformationStep` object, that has an `id`, an `origin`
and a `target` map of values in it. Per default, the target map is the same as the origin map.

The final data written in the `target` map will be written to the database. The `id` can not be changed.

### 4.3.7. Many-to-many relations
Of course, you can just define a many-to-many relation by defining two one-to-many relations, but this is not
the preferred way, as it will result in a lot of boilerplate code and developer responsibility.

Therefore, you can create a many-to-many tables by the `manyToMany` function in the `Evolution` object.

It takes two models, that should be related, and a block, that can be seen as the `execute` block
of an evolution a can define intermediate table data.

```kotlin
Evolution.manyToMany(User, Role) { // this: EvolutionExecutionStep
    enum<RoleUserType>("type")
}
```

This will create a table, that has a `user_id` and a `role_id` column, that are foreign keys to the `users`
and `roles` table, and a `type` column, that is an enum of the `RoleUserType` enum.

The `manyToMany` function will also create a reverse relation on both models, that can be used to retrieve
the related entities, more on that in the [ORM](ORM.md#5-orm) part.

## 4.4. Execute evolutions
To execute all found evolutions, you can use the `execute` function on the `Evolution` object. This will
execute all evolutions, that are not yet executed, in the order, that is defined by the `order` property.

To execute only specific evolutions (by their name), you can parse a vararg of names to the `execute` function.

```kotlin
Evolution.execute("create users table", "create sessions table")
```

In addition, you can parse a option `fresh` to the `execute` function, that will drop all tables before
executing the evolutions.

```kotlin
Evolution.execute(fresh = true)
```

To revert all evolutions, you can use the `revert` function on the `Evolution` object. This will
revert all evolutions, that are already executed, in the reverse order, that is defined by the `order` property.

To revert only specific evolutions (by their name), you can parse a vararg of names to the `revert` function.

```kotlin
Evolution.revert("create users table", "create sessions table")
```

The last function is the `wipe` function. This will drop all tables and data in your database,
ignoring if they are defined in the evolutions or not.

```kotlin
Evolution.wipe()
```

[Back: Configure and connect](ConfigureAndConnect.md#33-disconnect)