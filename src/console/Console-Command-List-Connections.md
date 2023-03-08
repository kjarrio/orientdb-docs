
# Console - `LIST CONNECTIONS`

Displays all active connections to the OrientDB Server.  Command introduced in version 2.2.
The connections as per server, so you should connect to the server, not to the database.

**Syntax**

```sql
LIST CONNECTIONS
```

**Permissions**

In order to enable a user to execute this command, you must add `"server.info"` as resource to the [server user](Console-Command-Set-Server-User.md).

**Example**

- List the current connections to the OrientDB Server:
  
  <pre>
  orientdb {server=remote:localhost/}> <code class='lang-sql userinput'>LIST CONNECTIONS</code>

  ---+----+--------------+------+-------------------+--------+-----+--------+--------
   # | ID |REMOTE_ADDRESS|PROTOC|LAST_OPERATION_ON  |DATABASE|USER |COMMAND |TOT_REQS
  ---+----+--------------+------+-------------------+--------+-----+--------+--------
   0 | 17 |/127.0.0.1    |binary|2015-10-12 19:22:34|-       |-    |info    | 1       
   1 | 16 |/127.0.0.1    |binary|1970-01-01 01:00:00|-       |-    |-       | 0       
   5 | 1  |/127.0.0.1    |http  |1970-01-01 00:59:59|pokec   |admin|Listen  | 32      
  ---+----+--------------+------+-------------------+--------+-----+--------+--------
  </pre>


>For more information on other commands, see [Console Commands](Console-Commands.md).
