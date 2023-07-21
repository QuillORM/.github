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
   3. [The key concept: Structure only](Intoduction.md#13-the-key-concept-structure-only)
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
4. [Table Structure](TableStructure.md#4-table-structure)
   1. [Versioning](TableStructure.md#41-versioning)
   2. [The table object](TableStructure.md#42-the-table-object)
      1. [IdTable](TableStructure.md#421-idtable)
      2. [IntIdTable](TableStructure.md#422-intidtable)
      3. [UUIDIdTable](TableStructure.md#423-uuididtable)
   3. [The table structure](TableStructure.md#43-the-table-structure)
   4. [Column versioning](TableStructure.md#44-column-versioning)
   5. [Transforming data](TableStructure.md#45-transforming-data)
   6. [Relations](TableStructure.md#46-relations)
      1. [oneToOne and oneToMany](TableStructure.md#461-onetoone-and-onetomany)
      2. [manyToMany](TableStructure.md#462-manytomany)
5. [ORM](ORM.md#5-orm)
   1. [The model](ORM.md#51-the-model)
      1. [The model interface](ORM.md#511-the-model-interface)
      2. [The model sql builder interface](ORM.md#512-the-model-sql-builder-interface)
      3. [The model companion object](ORM.md#513-the-model-companion-object)
      4. [The model implementation](ORM.md#514-the-model-implementation)
      5. [The model sql builder implementation](ORM.md#515-the-model-sql-builder-implementation)
   2. [Relations](ORM.md#52-relations)
      1. [The relation interface](ORM.md#521-the-relation-interface)
      2. [Many-to-many relations](ORM.md#522-many-to-many-relations)
6. [Model generation](ModelGeneration.md#6-model-generation)
   1. [The model generator](ModelGeneration.md#61-the-model-generator)
   2. [How to generate models](ModelGeneration.md#62-how-to-generate-models)
   3. [Model generator conclusion](ModelGeneration.md#63-model-generator-conclusion)
7. [Modules](Modules.md#7-modules)
8. [Conclusion](Conclusion.md#8-conclusion)

[Next: Introduction](Intoduction.md#1-introduction)