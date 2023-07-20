# 3. Configure and connect
As QuillORM aims to be "feather-light", a basic configuration is kept as simple as possible.

However, QuillORM allows you to configure almost everything to your needs. With that said, let's start
with the required part, the database connection.

## 3.1. Configuration
## 3.1.1. Basics
To start the configuration process, the `QuillORM` object has to be called. This object is the entry point for all
configuration options.

```kotlin
QuillORM { // this: QuillORMSetup
    // Configuration options
}
```

The invocation of the `QuillORM` object allows for a block that has the `QuillORMSetup` as its receiver.
With this receiver, all further configuration options can be called.

If the `QuillORM` object is invoked multiple times, the configuration options are merged together.
However, if the same option is set multiple times, the last one is used.

### 3.1.2. Database configuration
Databases are split into one primary and multiple secondary databases. The primary database is the database, 
that is used for all actions, that do not specify a database. Secondary databases are used for actions, that specify
a database.

To configure databases the `primary` or `secondary` function has to be called.
Both take blocks as there parameters, that have a `DatabaseSetup` as their receiver.

The `secondary` function takes in addition to the block a `name` parameter, that specifies the name of the database.

```kotlin
QuillORM { // this: QuillORMSetup
    primary { // this: DatabaseSetup
        // Primary database configuration
    }
    
    secondary("name") { // this: DatabaseSetup
        // Secondary database configuration
    }
}
```

The `DatabaseSetup` receiver has multiple ways to configure the database connection.

#### 3.1.2.1. Connection string
You can use a connection string to configure the database connection. The connection string needs to be a valid JDBC.
In addition, you can define a `username` and `password` to authenticate against the database,
as well as a `driver` to specify the JDBC driver to use (this should not be required for most newer drivers).

```kotlin
QuillORM { // this: QuillORMSetup
    primary { // this: DatabaseSetup
        this.url = "jdbc:postgresql://localhost:5432/quillorm"
        this.username = "quillorm"
        this.password = "quillorm"
        this.driver = "org.postgresql.Driver"
    }
}
```

#### 3.1.2.2. DataSource
You can also use a `DataSource` to configure the database connection. This is useful, if you want to use a connection
pool or if you want to use a different connection for each database action.

```kotlin
QuillORM { // this: QuillORMSetup
    primary { // this: DatabaseSetup
        this.dataSource = dataSource
    }
}
```

Or even easier, you can directly pass the `DataSource` to the `primary` or `secondary` function.

```kotlin
QuillORM { // this: QuillORMSetup
    primary(dataSource)
}
```

If both, a connection string and a `DataSource` are set, the `DataSource` is used.

#### 3.1.2.3. Dialect
Each database can have a different dialect.
A dialect describes the SQL syntax that is used by the database.
When no dialect is set, QuillORM tries to detect the dialect automatically.

```kotlin
QuillORM { // this: QuillORMSetup
    primary { // this: DatabaseSetup
        this.dataSource = dataSource
        this.dialect = Dialect.PostgreSQL
    }
}
```

When the dialect can not be detected automatically or no dialect exists for the database, the `Dialect.Generic` dialect
is used, which relies solely on the SQL syntax defined by the SQL standard.

QuillORM will come with a default set of dialects, which are:
- `Dialect.Generic` for the SQL standard
- `Dialect.H2` for H2
- `Dialect.MySQL` for MySQL
- `Dialect.MariaDB` for MariaDB
- `Dialect.Oracle` for Oracle
- `Dialect.PostgreSQL` for PostgreSQL

However, you can implement your own dialects, by extending the `Dialect` class.

### 3.1.3. Other configuration options
Any other configuration option is not required and can be set as needed.

In future chapters of the concept there will be some configuration options explained.

#### 3.1.3.1. Logging
With the logging configuration option, you can configure the logging behavior of QuillORM.

To configure, call the `logging` function on the `QuillORMSetup` receiver. 
This function takes a block as its parameter, that has a `LoggingSetup` as its receiver.

QuillORM uses [SLF4J](https://www.slf4j.org/) as its logging facade. 
Therefore, you can use any SLF4J compatible logging implementation.

```kotlin
QuillORM { // this: QuillORMSetup
    logging { // this: LoggingSetup
        // You can configure the logger that QuillORM should use
        // The default logger is the QuillORM logger
        this.logger = logger
        
        // You can configure that QuillORM should log all SQL statements executed (they will be logged at the debug level)
        // The default is false
        this.logSQL = true
        
        // You can also configure to include the parameters of the SQL statements in the log
        // This will only work, if logSQL is true
        // The default is false
        this.logSQLParameters = true
        
        // You can also configure to log the time required to execute the SQL statements
        // This will only work, if logSQL is true
        // The default is false
        this.logSQLTime = true
    }
}
```

## 3.2. Connect
When you have configured QuillORM, you can connect to the database.

To connect to the database, call the `connect` function on the `QuillORM` object.

```kotlin
QuillORM.connect()
```

With that all configuration options are set and QuillORM is ready to use.

## 3.3. Disconnect
When you are done with QuillORM, you can disconnect from the database.

To disconnect from the database, call the `disconnect` function on the `QuillORM` object.

```kotlin
QuillORM.disconnect()
```

This will close all active connections to the database and close a `DataSource` if one was used.

[Back: JDBC Shell](JDBCShell.md#2-jdbc-shell)