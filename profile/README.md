# QuillORM
The Feather-Light Quill for Kotlin Relational Mapping 🪶

## What is QuillORM?
QuillORM is a lightweight object relation mapping library for Kotlin.

With its **migrations only** approach, you as a developer can focus on the database structure,
the generation of models is done by QuillORM.

## How to use QuillORM?
QuillORM is currently in an early stage of development.

The following phases are planned:
1. 👉 Concept: In this phase, a concept for QuillORMs **migrations only** approach is developed.
2. Migrations: In this phase the migration engine is developed, 
to actually convert the created migrations to a database structure.
3. ORM: In this phase the actual ORM and therefore mapping between the database and the models is developed.
This is the base for the model generation, which will only rely on the ORM to do the heavy lifting.
4. Model generation: In this phase the model generation is developed, which will generate the models based on the
migrations.

## How to contribute?
If you want to contribute to QuillORM, you can do so by creating a pull request, opening an issue, starting a discussion or participating in existing ones.

However, in the current phase of development, where a concept is developed, we try to keep the team as small as possible,
to keep the basic view of QuillORM in sight and make development as fast as possible.

## Concept
You can view the current concept at [Concept.md](https://github.com/CheeseTastisch/QuillORM/blob/main/concept/Concept.md).
