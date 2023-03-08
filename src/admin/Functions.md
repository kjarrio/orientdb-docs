
# Functions

A **Function** is an executable unit of code that can take parameters and return a result. Using Functions you can perform [Functional programming](http://en.wikipedia.org/wiki/Functional_programming) where logic and data are all together in a central place. Functions are similar to the [Stored Procedures](http://en.wikipedia.org/wiki/Stored_procedure) of RDBMS.

>**NOTE**: This guide refers to the last available release of OrientDB. For past revisions look at [Compatibility](Functions.md#compatibility).

OrientDB Functions features:
- are persistent
- can be written in [SQL](../sql/README.md) or Javascript (Ruby, Scala, Java and other languages are coming)
- can be executed via [SQL](../sql/README.md), [Java](Functions-Use.md#functions-using-the-java-api), [REST](Functions-Use.md#functions-using-the-http-rest-api) and [Studio](https://github.com/orientechnologies/orientdb-studio/wiki)
- can call each other
- supports recursion
- have automatic mapping of parameters by position and name
- plugins can inject new objects to being used by functions


>The [OrientDB SQL](../sql/README.md) dialect supports many functions written in the native language.  To get better performance, you can write your own native functions in the Java language and register them to the engine. 
>
>For more information, see [Custom Functions in Java](../sql/SQL-Functions.md#custom-functions-in-java).


## Compatibility

### 1.5.0 and before

OrientDB binds the following variables:
- `db`, that is the current document database instance
- `gdb`, that is the current graph database instance
