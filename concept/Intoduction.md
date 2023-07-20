
# 1. Introduction
### 1.1. What is the goal of QuillORM?
QuillORM amis to be a "feather-light quill for Kotlin object relational mapping", but what does that mean?

The "feather-light" parts reassembles the aim of QuillORM to be a lightweight framework,
that requires as little configuration as only given the information for the database connection.

The "quill" stands for the aim to provide a fluent, easy to use API, that is easy to understand and takes over
the responsibility of the developer to write the SQL queries.

The "Kotlin" part is clear, QuillORM is written and designed for Kotlin.

The "object relational mapping" part is the most important part of QuillORM. QuillORM maps the date from your database
to data a developer can easily work with. This is done by **generating models** based on the database structure.

## 1.2. Why another ORM?
You may already know many Kotlin ORM frameworks: "Exposed", "Ktorm" and "Hybernate" only to name a few.

**So why another ORM?**

We, as the main developers of QuillORM have looked into many of these existing ORM frameworks,
but all of them have their downsides, we don't want to state, that QuillORM won't have downsides, but we think,
that existing ORMs do not make the right trade-offs for our use cases.

Some of the main downsides are:
- **Duplicate writing of structure**: Most ORMs required the developer to describe the database in two ways,
  as a schema and as models. This basically doubles the required work of the developer, without any benefit.
- **No migrations**: Most ORMs do not provide a way to migrate the database, which is a huge downside,
  as the database structure is not static and will change over time. For this reason, QuillORM introduces Evolutions,
  more on that later.
- **No model generation**: In most ORMs, as already mentioned, the developer has to write the models by hand,
  but the developer already has to write the schema, so why not generate the models from the schema?
  This provides some more benefits, but more on that also later.
- **Not Kotlin first**: Most ORMs are written in Java and therefore do not provide a fluent API for Kotlin.
  Kotlin has many nice and useful features, that can not be used when developing a library in Java,
  which is the case for many ORMs.

That are, among some others, the most important downsides of existing ORMs, that QuillORM tries to solve.

## 1.3. The key concept: Evolutions only
The key concept of QuillORM is that the developer is only required to write evolutions.

**But what are evolutions?**

Think of your database as a living creature, let's call it "Bob".
At the start of his life, Bob is a baby, 
he has no structure (no tables) and is basically empty (your database is blank).

But as time evolves, Bob grows up and gets more and more structure (tables),
in his first view years he learns most things,
that he will need for the rest of his life (your database gets a basic structure).

As Bob grows even older, he learns more and more things (your database gets more and more tables) and
improves on his existing skills (your existing tables transform).

Maybe Bob forgets some of his skills or does not need them anymore (some database tables get removed).

But Bob will never forget his basic skills (your database will never forget its basic structure).

When you view Bob on a timeline, you can see, that he evolves over time (your database evolves over time).

Another important thing to notice is that Bob can not die, as long as you let him live.

**So back to a more technical view...**

Basically what all of this means is, that your database is not static, it evolves over time and will change over time.
And that is what an evolution describes, a change in your database structure.

Evolution always have a `do` and a `undo` part, that describes how to apply and how to revert that evolution.

In addition, evolution can have a `transform` and a `revert` part, that describes how to transform the data of an
exiting table to the new structure and how to revert that transformation.

I think that's enough for the highly theoretical part, so let's get to more practical sides.

[Back: Table of Contents](Concept.md#table-of-contents) | [Next: JDBC Shell](JDBCShell.md#2-jdbc-shell)