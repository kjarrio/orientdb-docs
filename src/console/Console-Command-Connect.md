
<!-- proofread 2015-01-07 SAM -->

# Console - `CONNECT`

> (deprecated, see [CONNECT ENV](Console-Command-Connect-Env.md) and [OPEN](Console-Command-Open.md))

Opens a database or connects to a server.

**Syntax**

```
CONNECT <database-url> <user> <password>
```

- **`<database-url>`** Defines the URL of the database you want to connect to.  It uses the format `<mode>:<path>`
  - *`<mode>`* Defines the mode you want to use in connecting to the database.  It can be `plocal` or `remote`.
  - *`<path>`* Defines the path to the database.  
- **`<user>`** Defines the user you want to connect to the database with.
- **`<password>`** Defines the password needed to connect to the database, with the defined user.


**Examples:**

- Connect to a local database as the user `admin`, loading it directly into the console:

  <pre>
  orientdb> <code class="userinput lang-sql">connect plocal:../databases/GratefulDeadConcerts admin my_admin_password</code>

  Connecting to database [plocal:../databases/GratefulDeadConcerts]...OK
  </pre>

- Connect to a remote database:

  <pre>
  orientdb> <code class="lang-sql userinput">connect remote:192.168.1.1/GratefulDeadConcerts admin my_admin_password</code>

  Connecting to database [remote:192.168.1.1/GratefulDeadConcerts]...OK
  </pre>

>For more information on other commands, see [Console Commands](Console-Commands.md).
