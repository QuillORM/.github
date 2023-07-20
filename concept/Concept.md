# Preamble
The concept of QuillORM is still in an early phase of development. Therefore, the concept is currently only a
concept proposal. The concept is neither complete nor final or even implemented.

The concept has the intention to provide an overview of the planned functionality and the planned structure of
QuillORM.

Feel free to discuss the concept with us in our [GitHub Discussions](https://github.com/orgs/QuillORM/discussions).

As the concept gets implemented, the concept will be removed and replaced with a documentation,
that will be available in its own repository.

The concept is split into multiple files, that are located in the `concept` folder.
The purpose of these split is to keep a better overview of the concept.
In general each topic has its own file.

# Table of Contents
1. [Introduction](Intoduction.md#1-introduction)
   1. [What is the goal of QuillORM?](Intoduction.md#11-what-is-the-goal-of-quillorm)
   2. [Why another ORM?](Intoduction.md#12-why-another-orm)
   3. [The key concept: Evolutions only](Intoduction.md#13-the-key-concept-evolutions-only)
2. [JDBC Shell](JDBCShell.md#2-jdbc-shell)
   1. [Creating a Shell](JDBCShell.md#21-creating-a-shell)
   2. [Executing SQL statements](JDBCShell.md#22-executing-sql-statements)
   3. [Use the result](JDBCShell.md#23-use-the-result)
   4. [Building SQL statements](JDBCShell.md#24-building-sql-statements)
3. [Configure and connect](ConfigureAndConnect.md#3-configure-and-connect)
   1. [Configuration](ConfigureAndConnect.md#31-configuration)
      1. [Basics](ConfigureAndConnect.md#311-basics)
      2. [Database configuration](ConfigureAndConnect.md#312-database-configuration)
      3. [Other configuration options](ConfigureAndConnect.md#313-other-configuration-options)
   2. [Connect](ConfigureAndConnect.md#32-connect)
   3. [Disconnect](ConfigureAndConnect.md#33-disconnect)
4. [Evolutions](Evolutions.md#4-evolutions)
   1. [Why evolutions?](Evolutions.md#41-why-evolutions)
   2. [Basics](Evolutions.md#42-basics)
      1. [What are evolutions?](Evolutions.md#421-what-are-evolutions)
      2. [How do evolutions work?](Evolutions.md#422-how-do-evolutions-work)
   3. [Writing evolutions](Evolutions.md#43-writing-evolutions)
      1. [Basics](Evolutions.md#431-basics)
      2. [Your first evolution](Evolutions.md#432-your-first-evolution)
      3. [Ordering evolutions](Evolutions.md#433-ordering-evolutions)
      4. [Execute and undo](Evolutions.md#434-execute-and-undo)
      5. [Relations](Evolutions.md#435-relations)
      6. [Transform and revert](Evolutions.md#436-transform-and-revert)
      7. [Many-to-many relations](Evolutions.md#437-many-to-many-relations)
5. [ORM](ORM.md#5-orm)
6. [Model generation](ModelGeneration.md#6-model-generation)
7. [Modules](Modules.md#7-modules)
8. [Conclusion](Conclusion.md#8-conclusion)

[Next: Introduction](Intoduction.md#1-introduction)