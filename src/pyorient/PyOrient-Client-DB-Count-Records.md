
# PyOrient Client - `db_count_records()`

This method allows you to get the number of records in an opened OrientDB database.

## Counting Records

There are two methods available in determining the size of the opened database.  [`db_size()`](PyOrient-Client-DB-Size.md) returns the size of the database, while `db_count_records()`, which returns the number of records in the database.  It takes no arguments and returns a long value.

**Syntax**

```
client.db_count_records()
```

**Example**

You might use this method as a basic sanity check in your application.  For instance, you might use [`db_exists()`](PyOrient-Client-DB-Exists.md) to determine whether the database was created, then you can use the `db_count_records()` to determine whether or not the database is empty.

```py
# Check that Database exists and contains Data
if client.db_exists("tinkerhome"):
   assert client.db_count_records() != 0
```
