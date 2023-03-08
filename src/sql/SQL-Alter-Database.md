
# SQL - `ALTER DATABASE`

Updates attributes on the current database.

**Syntax**

```sql
ALTER DATABASE <attribute-name> <attribute-value>
```

- **`<attribute-name>`** Defines the attribute that you want to change.  For a list of supported attributes, see the section below.
- **`<attribute-value>`** Defines the value you want to set.


**Examples**

- Disable new SQL strict parser:

  <pre>
  orientdb> <code class="lang-sql userinput">ALTER DATABASE CUSTOM strictSQL=false</code>
  </pre>

- Update a Graph database that was created before version 1.4:

  <pre>
  orientdb> <code class='lang-sql userinput'>ALTER DATABASE CUSTOM useLightweightEdges=FALSE</code>
  orientdb> <code class='lang-sql userinput'>ALTER DATABASE CUSTOM useClassForEdgeLabel=FALSE</code>
  orientdb> <code class='lang-sql userinput'>ALTER DATABASE CUSTOM useClassForVertexLabel=FALSE</code>
  orientdb> <code class='lang-sql userinput'>ALTER DATABASE CUSTOM useVertexFieldsForEdgeLabel=FALSE</code>
  </pre>

  >Version 1.4 introduced [Lightweight Edges](../java/Lightweight-Edges.md), which was disabled by default beginning in version 2.0.  Use the above commands to maintain compatibility when using older databases with newer versions of OrientDB.




>To create a database, see the [`CREATE DATABASE`](../console/Console-Command-Create-Database.md).  To remove a database, see the [`DROP DATABASE`](../console/Console-Command-Drop-Database.md) command.  
>For more information on other commands, please refer to [Console Commands](../console/Console-Commands.md) and [SQL Commands](SQL-Commands.md).



## Supported Attributes

- **STATUS** (String) database's status between:
 - **"CLOSED"** to set closed status
 - **"IMPORTING"** to set importing status
 - **"OPEN"** to set open status
- **DEFAULTCLUSTERID** to set the default cluster. By default is 2 = "default"
- **DATEFORMAT** (String) sets the default date format. Look at [Java Date Format](http://docs.oracle.com/javase/6/docs/api/java/text/SimpleDateFormat.html) for more information. Default is "yyyy-MM-dd"
- **DATETIMEFORMAT** (String) sets the default date time format. Look at [Java Date Format](http://docs.oracle.com/javase/6/docs/api/java/text/SimpleDateFormat.html) for more information. Default is "yyyy-MM-dd HH:mm:ss"
- **TIMEZONE** (String) set the default timezone. Look at [Java Date TimeZones](http://docs.oracle.com/javase/6/docs/api/java/util/TimeZone.html) for more information. Default is the JVM's default timezone
- **LOCALECOUNTRY** (String) sets the default locale country. Look at [Java Locales](http://docs.oracle.com/javase/6/docs/api/java/util/Locale.html) for more information. Default is the JVM's default locale country. Example: "GB"
- **LOCALELANGUAGE** (String) sets the default locale language. Look at [Java Locales](http://docs.oracle.com/javase/6/docs/api/java/util/Locale.html) for more information. Default is the JVM's default locale language. Example: "en"
- **CHARSET** (String) set the default charset charset. Look at [Java Charset](http://docs.oracle.com/javase/6/docs/api/java/nio/charset/Charset.html) for more information. Default is the JVM's default charset. Example: "utf8"
- **CLUSTERSELECTION** (String) sets the default strategy used on selecting the cluster where to create new records. This setting is read on class creation. After creation, each class has own modifiable strategy. Supported strategies are:
 - **"default"**, uses always the Class's ```defaultClusterId``` property. This was the default before 1.7
 - **"round-robin"**, put the Class's configured clusters in a ring and returns a different cluster every time restarting from the first when the ring is completed
 - **"balanced"**, checks the records in all the clusters and returns the smaller cluster. This allows the cluster to have all the underlying clusters balanced on size. On adding a new cluster to an existent class, the new empty cluster will be filled before the others because more empty then the others. In distributed configuration when configure clusters on different servers this setting allows to keep the server balanced with the same amount of data. Calculation of cluster size is made every 5 or more seconds to avoid to slow down insertion
- **MINIMUMCLUSTERS** (Integer), as the minimum number of clusters to create automatically when a new class is created. By default is 1, but on multi CPU/core having multiple clusters/files improves read/write performance
- **CONFLICTSTRATEGY**, (since v2.0) is the name of the strategy used to handle conflicts when OrientDB's MVCC finds an update or delete operation executed against an old record. The strategy is applied for the entire database, but single clusters can have own strategy (use [ALTER CLUSTER](SQL-Alter-Cluster.md) command for this). While it's possible to inject custom logic by writing a Java class, the out of the box modes are:
 - `version`, the default, throw an exception when versions are different
 - `content`, in case the version is different checks if the content is changed, otherwise use the highest version and avoid throwing exception
 - `automerge`, merges the changes
- **CUSTOM** sets custom properties
- **VALIDATION**, (Since v2.2) disable or enable the validation for the entire database. This setting is not persistent, so at the next restart the validation is active (Default). Disabling the validation sometimes is needed in case of [remote import database](../console/Console-Command-Import.md#validation-errors).



## History
### 1.7
- Adds support for `CLUSTERSELECTION` that sets the strategy used on selecting the cluster where to create new records.
- Adds `MINIMUMCLUSTERS` to pre-create X clusters every time a new class is created.

