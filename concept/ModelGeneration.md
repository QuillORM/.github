# 6. Model generation
The ORM itself is the base of QuillORM.
But with it alone, we would terribly fail at our goal to make the developer focus on the database structure.

With the model generation alone, the developer would have even more work to do, than with a traditional ORM.
The developer would be required to write, alongside the models, sql builders and their implementations.

Therefore, we need a way to generate the models, sql builders and their implementations.

## 6.1. The model generator
The model generator is the part of QuillORM, that generates the models, sql builders and their implementations.

It is a gradle task, that the developer should execute after every change to the table structure.

That gradle task will look for table structures in the project and generate the models, sql builders and
their implementations in a package, that the developer can specify.

We suggest some package like `at.tld.generated.models`, to make it clear, that the models are generated.

## 6.2. How to generate models
The model generate will generate the all structures only based on the table structure and
the configured `targetVersion` of the table structure.

For this, the model generate will search for objects that implement the `IdTable`.
Then the generated will evolve that tables in a local cache.

From that local cache on, the model generate will generate two files for each table:
1. A model file, that contains the model interface, the sql builder interface and the companion object.
2. A model implementation file, that contains the implementation of the other file.

The implementation file will be generated in a subpackage, per default `impl`, of the package,
that the model file is in.

Additional interfaces or implementations, such as the relation interfaces will be generated in the same file,
as the model, they are required by.

## 6.3. Model generator conclusion
The model generator is currently only an abstract concept.
To really write a concept for it, that would describe the implementation of it,
the [TableStructure](TableStructure.md) and the [ORM](ORM.md) need to be implemented first.

[Back: ORM](ORM.md#522-many-to-many-relations) | [Next: Modules](Modules.md#7-modules)