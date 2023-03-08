
# Server Security

Individual OrientDB servers can manage multiple databases at a time and each database can have its own set of users.  When using OrientDB through the HTTP protocol, the OrientDB server uses one realm per database.

|   |   |
|---|---|
| ![](../images/warning.png) | While OrientDB can function as a regular Web Server, it is not recommended that you expose it directly to the internet or to public networks.  Instead, always hide the OrientDB server within a private network. |

Server users are stored in the `config/orientdb-server-config.xml` configuration file, in the `<users>` element.

```xml
    <users>
        <user name="root" password="{PBKDF2WithHmacSHA256}55F3DEAE:DLJEJFDKY8:65536" resources="*" />
        <user name="guest" password="{PBKDF2WithHmacSHA256}B36E7993C961:C8C8B36F3:65536" resources="connect,server.listDatabases,server.dblist" />
    </users>
```

When the OrientDB server starts for the first time, it creates the user `root` automatically, by asking you to give the password in the terminal.  In the event that you do not specify a password, OrientDB generates a random password.  Beginning with version 2.2, OrientDB hashes the passwords using `PBKDF2WithHmacSHA256` algorithm if it's running on Java 8 or major, otherwise `PBKDF2WithHmacSHA1` with a configurable SALT.

For more information on security in Orientdb, see:
- [Database security](Database-Security.md)
- [Database Encryption](Database-Encryption.md)
- [Secure SSL connections](Using-SSL-with-OrientDB.md)

## Configuration

While the default users and passwords are fine while you are setting your system up, it would be inadvisable to leave them in production.  To help restrict untrusted  users from accessing the OrientDB server, add a new user and change the passwords in the `config/orientdb-server-config.xml` server configuration file.  

To restrict unauthorized users from giving themselves privileges on the OrientDB server, disable write-access to the configuration file.  To help prevent them from viewing passwords, disable read-access as well.  Note that even if the passwords are hashed, there are many techniques available to crack the hash or otherwise guess the real password.


|   |   |
|---|---|
|![](../images/warning.png)|It is strongly recommended that you allow read/write access to the entire `config` directory only to the user that starts the OrientDB server.|


## Managing Users

Beginning with version 2.2, the OrientDB console provides a series of commands for managing users:

- [`LIST SERVER USERS`](../console/Console-Command-List-Server-Users.md): Displays all users.
- [`SET SERVER USER`](../console/Console-Command-Set-Server-User.md): Creates or modifies a user.
- [`DROP SERVER USER`](../console/Console-Command-Drop-Server-User.md): Drops a user.


## Server Resources

Each user can declare which resources have access.  The wildcard `*` grants access to any resource.  By default, the user `root` has all privileges (`*`), so it can access all the managed databases and operations.

| Resources | Permission to |
|-----------|-------------|
|`database.create`|Create a new database in the server|
|`database.drop`|Drop a database|
|`database.exists`|Check the existence of a database|
|`database.freeze`|Freeze the access to a database|
|`database.release`|Release a frozen database|
|`database.passthrough`|Allow access to all managed databases|
|`server.config.get`|Retrieve server's setting|
|`server.config.set`|Update server's setting|
|`server.connect`|Connect to a server|
|`server.info`|Retrieve server's information and statistics|
|`server.listDatabases`|Enlist available databases on the server|
|`server.listDatabases.system`|Include the OSystem database in the list of databases|
|`server.replication`|Execute a replication command from another server|
|`server.replication.start`|Start the replication of a database|
|`server.replication.stop`|Stop the replication of a database|
|`server.replication.config`|Update the replication configuration|
|`server.shutdown`|Shutdown a server|

For example,

```xml
<user name="replicator" password="repl" resources="database.passthrough"/>
```



## Securing Connections with SSL

Beginning with version 1.7, you can further improve security on your OrientDB server by securing connections with SSL.  For more information on implementing this, see [Using SSL](Using-SSL-with-OrientDB.md).


## Restoring the User admin 

In the event that something happens and you drop the class `OUser` or the user `admin`, you can use the following procedure to restore the user to your database.

1. Ensure that the database is in the OrientDB server database directory, `$ORIENTDB_HOME/database/ folder`.

1. Launch the console or studio and log into the database with the user `root`.

   <pre>
   $ <code class="lang-sh userinput"> $ORIENTDB_HOME/bin/console.sh</code>

   OrientDB console v.X.X.X (build 0) www.orientdb.com
   Type 'HELP' to display all the commands supported.
   Installing extensions for GREMLIN language v.X.X.X

   orientdb> <code class="lang-sql userinput">CONNECT remote:localhost/my_database root rootpassword</code>
   </pre>

1. Check that the class `OUser` exists:

   <pre>
   orientdb> <code class="lang-sql userinput">SELECT FROM OUser WHERE name = 'admin'</code>
   </pre>

   - In the event that this command fails because the class `OUser` doesn't exist, create it:

     <pre>
     orientdb> <code class="lang-sql userinput">CREATE CLASS OUser EXTENDS OIdentity</code>
     </pre>

   - In the event that this command fails because the class `OIdentity doesn't exist, create it first:

     <pre>
     orinetdb> <code class="lang-sql userinput">CREATE CLASS OIdentity</code>
     </pre>

     Then repeat the above command, creating the class `OUser`

1. Check that the class `ORole` exists.

   <pre>
   orientdb> <code class="lang-sql userinput">SELECT FROM ORole WHERE name = 'admin'</code>
   </pre>

   - In the event that the class `ORole` doesn't exist, create it:

     <pre>
     orientdb> <code class="lang-sql userinput">CREATE CLASS ORole EXTENDS OIdentity</code>
     </pre>

1. In the event that the user or role `admin` doesn't exist, run the following commands:

   - In the event that the role `admin` doesn't exist, create it:

     <pre>
     orientdb> <code class="lang-sql userinput">INSERT INTO ORole SET name = 'admin', mode = 1, 
               rules = { "database.bypassrestricted": 15 }</code>
     </pre>

   - In the event that the user `admin` doesn't exist, create it:

     <pre>
     orientdb> <code class="lang-sql userinput">INSERT INTO OUser SET name = 'admin', 
               password = 'my-admin_password', status = 'ACTIVE', 
               rules = ( SELECT FROM ORole WHERE name = 'admin' )</code>
     </pre>

The user `admin` is now active again on your database.

## Restoring the Server's User root 

1. Open the `config/orientdb-server-config.xml` file and remove the "root" user entry
2. Remove the tag `<isAfterFirstTime>true</isAfterFirstTime>` at the end of the file
3. On next restart of the server, a new root password is asked again
