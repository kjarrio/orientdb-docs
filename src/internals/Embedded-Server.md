
# Embed the Server

Embedding an OrientDB Server inside a Java application has several advantages and interesting features:
- Java application that runs embedded with the server can bypass the remote connection and use the database directly with [local mode](../datamodeling/Concepts.md#database-url). local and remote connections against the same database can work in concurrency: OrientDB will synchronize the access.
- You can use the [Console](../console/README.md) to control it
- You can use the [OrientDB Studio](../studio/README.md)
- You can replicate the database across distributed standalone or embedded servers

To embed an OrientDB Server inside a Java application you have to create the [`OServer`](../java/ref/OServer.md) object and use a valid configuration for it.

## Requirements

In order to embed the server you need to include the following jar files in the classpath:
- <code>orientdb-client-**.jar</code>
- <code>orientdb-core-**.jar</code>
- <code>orientdb-server-**.jar</code>
- <code>orientdb-tools-**.jar</code>
- <code>concurrentlinkedhashmap-lru-**.jar</code>
- <code>jna-**.jar</code>
- <code>lz4-java-**.jar</code>

Starting from version 2.2, please set the `MaxDirectMemorySize` parameter. Setting this parameter is required. You can set it to a very high value, e.g. 512g (JVM setting):

```
-XX:MaxDirectMemorySize=512g
```

Setting `MaxDirectMemorySize` to a very high value should not concern you as it does not mean that OrientDB will consume all 512GB of memory. The size of direct memory consumed by OrientDB is limited by the size of the disk cache (variable [`storage.diskCache.bufferSize`](../admin/Configuration.md)).

When you start the Server using the provided `server.sh` or `server.bat` scripts, `MaxDirectMemorySize` is set already by those scripts. But when you embed the Server it is required that you set `MaxDirectMemorySize` manually.

Note: if you are using a pom file, you may set `MaxDirectMemorySize` inside your pom in the following way:

```
<properties>
   <argLine>-XX:MaxDirectMemorySize=512g</argLine>
</properties>
```

If you start an embedded Server without setting this variable you will get a WARNING message similar to the following:

```
MaxDirectMemorySize JVM option is not set or has invalid value, that may cause out of memory errors
```

## Include the commands you need

Even if most of the HTTP commands are auto registered assure to have all the commands you need. For example the static content must be registered. This is fundamental if you want to use OrientDB as Web Server providing static content like the Studio app:
```xml
<listener protocol="http" port-range="2480-2490" ip-address="0.0.0.0">
  <commands>
    <command implementation="com.orientechnologies.orient.server.network.protocol.http.command.get.OServerCommandGetStaticContent" pattern="GET|www GET|studio/ GET| GET|*.htm GET|*.html GET|*.xml GET|*.jpeg GET|*.jpg GET|*.png GET|*.gif GET|*.js GET|*.css GET|*.swf GET|*.ico GET|*.txt GET|*.otf GET|*.pjs GET|*.svg">
      <parameters>
        <entry value="Cache-Control: no-cache, no-store, max-age=0, must-revalidate\r\nPragma: no-cache" name="http.cache:*.htm *.html"/>
        <entry value="Cache-Control: max-age=120" name="http.cache:default"/>
      </parameters>
    </command>
  </commands>
</listener>
```

## Use an embedded configuration

```java
import com.orientechnologies.orient.server.OServerMain;

public class OrientDBEmbeddable {

 public static void main(String[] args) throws Exception {
  OServer server = OServerMain.create();
  server.startup(
   "<?xml version=\"1.0\" encoding=\"UTF-8\" standalone=\"yes\"?>"
   + "<orient-server>"
   + "<network>"
   + "<protocols>"
   + "<protocol name=\"binary\" implementation=\"com.orientechnologies.orient.server.network.protocol.binary.ONetworkProtocolBinary\"/>"
   + "<protocol name=\"http\" implementation=\"com.orientechnologies.orient.server.network.protocol.http.ONetworkProtocolHttpDb\"/>"
   + "</protocols>"
   + "<listeners>"
   + "<listener ip-address=\"0.0.0.0\" port-range=\"2424-2430\" protocol=\"binary\"/>"
   + "<listener ip-address=\"0.0.0.0\" port-range=\"2480-2490\" protocol=\"http\"/>"
   + "</listeners>"
   + "</network>"
   + "<users>"
   + "<user name=\"root\" password=\"ThisIsA_TEST\" resources=\"*\"/>"
   + "</users>"
   + "<properties>"
   + "<entry name=\"orientdb.www.path\" value=\"C:/work/dev/orientechnologies/orientdb/releases/1.0rc1-SNAPSHOT/www/\"/>"
   + "<entry name=\"orientdb.config.file\" value=\"C:/work/dev/orientechnologies/orientdb/releases/1.0rc1-SNAPSHOT/config/orientdb-server-config.xml\"/>"
   + "<entry name=\"server.cache.staticResources\" value=\"false\"/>"
   + "<entry name=\"log.console.level\" value=\"info\"/>"
   + "<entry name=\"log.file.level\" value=\"fine\"/>"
   //The following is required to eliminate an error or warning "Error on resolving property: ORIENTDB_HOME"
   + "<entry name=\"plugin.dynamic\" value=\"false\"/>"
   + "</properties>" + "</orient-server>");
  server.activate();
  }
}
```
Once the embedded server is running, clients can connect using the remote connection method.  For example in the console, you can connect with:
```
connect remote:localhost:{port}/{db} {user} {password}
where:
  port     : the port that the binary server listens on
             (first free port from 2424-2430 according to the configuration above)
  db       : the database name to connect to (defaults to "db" and can be set using <entry name="server.database.path" value="db"/> in the configuration
  user     : the user to connect with (this is NOT the same as root user in the configuration)
  password : the user to connect with (this is NOT the same as root password in the configuration)
```
## Use custom file for configuration

Use a regular `File`:
```java
public class OrientDBEmbeddable {
  public static void main(String[] args) throws Exception {
      OServer server = OServerMain.create();
      server.startup(new File("/usr/local/temp/db.config"));
      server.activate();
  }
}
```

## Use a stream for configuration

Use an `InputStream` from the class loader:
```java
public class OrientDBEmbeddable {
  public static void main(String[] args) throws Exception {
      OServer server = OServerMain.create();
      server.startup(getClass().getResourceAsStream("db.config"));
      server.activate();
  }
}
```

## Use a OServerConfiguration object for configuration

Or an `InputStream` from the class loader:
```java
public class OrientDBEmbeddable {
  public static void main(String[] args) throws Exception {
      OServer server = OServerMain.create();
      OServerConfiguration cfg = new OServerConfiguration();
      // FILL THE OServerConfiguration OBJECT
      server.startup(cfg);
      server.activate();
  }
}
```

## Shutdown

OrientDB Server creates some threads internally as non-daemon, so they run even if the main application exits. Use the [`OServer.shutdown()`](../java/ref/OServer/shutdown.md) method to shutdown the server in soft way:
```java
import com.orientechnologies.orient.server.OServerMain;

public class OrientDBEmbeddable {

  public static void main(String[] args) throws Exception {
    OServer server = OServerMain.create();
    server.startup(new File("/usr/local/temp/db.config"));
    server.activate();
    ...
    server.shutdown();
  }
}
```

## Setting ORIENTDB_HOME

Some functionality will not work properly if the system property 'ORIENTDB_HOME' is not set. You can set it programmatically like this:
```java
import com.orientechnologies.orient.server.OServerMain;

public class OrientDBEmbeddable {

  public static void main(String[] args) throws Exception {
    String orientdbHome = new File("").getAbsolutePath(); //Set OrientDB home to current directory
    System.setProperty("ORIENTDB_HOME", orientdbHome);

    OServer server = OServerMain.create();
    server.startup(cfg);
    server.activate();
  }
}
```
