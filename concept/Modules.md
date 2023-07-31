# 7. Modules
The whole system, as described in this, very abstract, concept would be highly extensible.

## 7.1. Extending the table structure
The table structure concept could be extended by creating your own `ColumnDescription`s,
or by adding extension functions to the `Column<T>`.

## 7.2. Extending models
Models could be extended just by adding an extension property or function to that model.
It could use any of the existing properties or functions of the model.

## 7.3. Extending the model generator
The model generator would be the hardest part to extend, 
as it would require some extension at the execution of the grade task.

However, as the model generator is currently the most abstract part of the system,
we can assume, that this is a problem, that can be solved.

How exactly this would be done, can be seen when the model generator is implemented.

[Back: Model generation](ModelGeneration.md#63-model-generator-conclusion) | [Next: Conclusion](Conclusion.md#8-conclusion)