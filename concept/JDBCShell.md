# 2. JDBC Shell

Do make working with JDBC easier, QuillORM provides a separated `jdbc-shell` module.

The JDBC Shells task is not to keep the writing of SQL statements away from the developer (that's the task of the ORM),
but to make the handling of results and building of SQL statements easier.

## 2.1. Creating a Shell
To create a `JDBCShell`, a `DataSource` or `Connection` is required.

```kotlin
val shell = JDBCShell(dataSource)
```

## 2.2. Executing SQL statements
Do execute a SQL statement, the `execute` method is used.

```kotlin
shell.execute("CREATE TABLE users (id INT PRIMARY KEY, name VARCHAR(255))")
```

The `execute` method returns a `StatementResult`, that contains the result of the execution and errors, if any occurred.

## 2.3. Use the result
The `StatementResult` contains the result of the execution, the metadata of it and errors, if any occurred.

```kotlin
shell.execute("SELECT * FROM users")
    .onSuccess { result -> /* ... */ }
    .onError { error -> /* ... */ }
``` 

The result on its own is a `ResultList`. 
The `ResultList` contains the metadata (`ResultMetadata`) and the rows (`ResultRows`) of the result.
A `ResultRows` is the representation of a row in the result.

```kotlin
shell.execute("SELECT * FROM users")
    .onSuccess { result ->
        val names = result.rows
            .map { row -> row.getString("name") }
        
        val metadata = result.metadata
    }
    .onError { error -> /* ... */ }
```

The `ResultMetadata` contains the metadata wrapped of the result, like the column names and types.

```kotlin
shell.execute("SELECT * FROM users")
    .onSuccess { result ->
        val metadata = result.metadata
        val columnNames = metadata.columnNames
        val columnTypes = metadata.columnTypes
    }
    .onError { error -> /* ... */ }
```

The `error` is a `SQLException` that contains the error produced by the execution of the SQL statement.

## 2.4. Building SQL statements
To build SQL Statements, the `SQLStatement` and `SQLStatementBuilder` are used.

The `SQLStatement` is the representation of prepared SQL statements.

```kotlin
shell.exeucte(
    SQLStatement("SELECT * FROM users WHERE id = ?")
        .set(1, 1)
)
```

The `SQLStatementBuilder` is used to build SQL statements.

```kotlin
val statement = buildSql {
    append("SELECT users.name")
    append("FROM users")
    append("WHERE users.id = ?", 1)
}
```

The exact definitions of the JDBC Shell may be changed during development.
So this is just a brief overview of the JDBC Shell.

[Back: Introduction](Intoduction.md#1-introduction) |
[Next: Configure and connect](ConfigureAndConnect.md#3-configure-and-connect)