
# SQL - `OPTIMIZE DATABASE`

Optimizes the database for particular operations. 

**Syntax**

```sql
OPTIMIZE DATABASE [-lwedges] [-noverbose]
```

- **`-lwedges`** Converts regular edges into Lightweight Edges.
- **`-noverbose`** Disables output.

>Currently, this command only supports optimization for [Lightweight Edges](../java/Lightweight-Edges.md).  Additional optimization options are planned for future releases of OrientDB.

**Examples**

- Convert regular edges into Lightweight Edges:

  <pre>
  orientdb> <code class="lang-sql userinput">OPTIMIZE DATABASE -lwedges</code>
  </pre>

>For more information, see
>- [Lightweight Edges](../java/Lightweight-Edges.md)
>- [SQL Commands](SQL-Commands.md)
>- [Console Commands](../console/Console-Commands.md)
