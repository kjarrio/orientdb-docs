---
search:
   keywords: ["tutorial", "distributed database", "graph"]
---

# Working with Distributed Graphs

When OrientDB joins a distributed cluster, all clients connecting to the server node are constantly notified about this state.  This ensures that, in the event that server node fails, the clients can switch transparently to the next available server.

You can check this through the console.  When OrientDB runs in a distributed configuration, the current cluster shape is visible through the [`INFO`](../../console/Console-Command-Info.md) command.


<pre>
$ <code class="lang-sh userinput">$ORIENTDB_HOME/bin/console.sh</code>

OrientDB console v.1.6 www.orientechnologies.com
Type 'help' to display all the commands supported.
Installing extensions for GREMLIN language v.2.5.0-SNAPSHOT


orientdb> <code class="lang-sql userinput">CONNECT remote:localhost/GratefulDeadConcerts admin admin</code>

Connecting to database [remote:localhost/GratefulDeadConcerts] with user 'admin'...OK


orientdb> <code class="lang-sql userinput">INFO</code>

Current database: GratefulDeadConcerts (url=remote:localhost/GratefulDeadConcerts)
</pre>

For reference purposes, the server nodes in the example have the following configurations.  As you can see, it is a two node cluster running a single server host.  The first node listens on port `2481` while the second on port `2480`.

```json
+---------+------+-----------------------------------------+-----+---------+--------------+--------------+-----------------------+
|Name     |Status|Databases                                |Conns|StartedOn|Binary        |HTTP          |UsedMemory             |
+---------+------+-----------------------------------------+-----+---------+--------------+--------------+-----------------------+
|europe-0 |ONLINE|distributed-node-deadlock=ONLINE (MASTER)|5    |16:53:59 |127.0.0.1:2424|127.0.0.1:2480|269.32MB/3.56GB (7.40%)|
|europe-1 |ONLINE|distributed-node-deadlock=ONLINE (MASTER)|4    |16:54:03 |127.0.0.1:2425|127.0.0.1:2481|268.89MB/3.56GB (7.38%)|
+---------+------+-----------------------------------------+-----+---------+--------------+--------------+-----------------------+
```

## Testing Distributed Architecture

Once you have a distributed database up and running, you can begin to test its operations on a running environment.  For example, begin by creating a vertex, setting the `node` property to `1`.

<pre>
orientdb> <code class="lang-sql userinput">CREATE VERTEX V SET node = 1</code>

Created vertex 'V#9:815{node:1} v1' in 0,013000 sec(s).
</pre>

From another console, connect to the second node and execute the following command:


<pre>
orinetdb> <code class="lang-sql userinput">SELECT FROM V WHERE node = 1</code>

----+--------+-------+
 #  | @RID   | node  |
----+--------+-------+
 0  | #9:815 | 1     |
----+--------+-------+
1 item(s) found. Query executed in 0.19 sec(s).
</pre>

This shows that the vertex created on the first node has successfully replicated to the second node.

## Logs in Distributed Architecture

From time to time server nodes go down.  This does not necessarily relate to problems in OrientDB, (for instance, it could originate from limitations in system resources).

To test this out, kill the first node.  For example, assuming the first node has a process identifier, (that is, a PID), of `1254` on your system, run the following command:

<pre>
$ <code class="lang-sh userinput">kill -9 1254</code>
</pre>

This command kills the process on PID `1254`.  Now, check the log messages for the second node:


<pre>
$ <code class="lang-sh userinput">less orientdb.log</code>

INFO [127.0.0.1]:2435 [orientdb] Removing Member [127.0.0.1]:2434
     [ClusterService]
INFO [127.0.0.1]:2435 [orientdb]
<code class="lang-json">Members [1] {
	Member [127.0.0.1]:2435 this
}</code>
 [ClusterService]
WARN [europe-0] node removed id=Member [127.0.0.1]:2434
     name=europe-1 [OHazelcastPlugin]
INFO [127.0.0.1]:2435 [orientdb] Partition balance is ok, no need to
     re-partition cluster data...  [PartitionService]
</pre>

What the logs show you is that the second node is now aware that it cannot reach the first node.  You can further test this by running the console connected to the first node..

<pre>
orientdb> <code class="lang-sql userinput">SELECT FROM V LIMIT 2</code>

WARN Caught I/O errors from /127.0.0.1:2425 (local
     socket=0.0.0.0/0.0.0.0:51512), trying to reconnect (error:
	 java.io.IOException: Stream closed) [OStorageRemote]
WARN Connection re-acquired transparently after 30ms and 1 retries: no errors
     will be thrown at application level [OStorageRemote]
---+------+----------------+--------+--------------+------+-----------------+-----
 # | @RID | name        | song_type | performances | type | out_followed_by | ...
---+------+----------------+--------+--------------+------+-----------------+-----
 1 | #9:1 | HEY BO DIDDLEY | cover  | 5            | song | [5]             | ...
 2 | #9:2 | IM A MAN       | cover  | 1            | song | [2]             | ...
---+------+----------------+--------+--------------+------+-----------------+-----
</pre>

This shows that the console auto-switched to the next available node.  That is, it switched to the second node upon noticing that the first was no longer functional.  The warnings reports show what happened in a transparent way, so that the application doesn't need to manage the issue.

From the console connected to the second node, create a new vertex.

<pre>
orientdb> <code class="lang-sql userinput">CREATE VERTEX V SET node=2</code>

Created vertex 'V#9:816{node:2} v1' in 0,014000 sec(s).
</pre>

Given that the first node remains nonfunctional, OrientDB journals the operation.  Once the first node comes back online, the second node synchronizes the changes into it.

Restart the first node and check that it successfully auto-realigns.  Reconnect the console to the first node and run the following command:

<pre>
orientdb> <code class="lang-sql userinput">SELECT FROM V WHERE node=2</code>

---+--------+-------+
 # | @RID   | node  |
---+--------+-------+
 0 | #9:816 | 2     |
---+--------+-------+
1 item(s) found. Query executed in 0.209 sec(s).
</pre>

This shows that the first node has realigned itself with the second node.

This process is repeatable with N server nodes, where every server is a master.  There is no limit to the number of running servers.  With many servers spread across a slow network, you can tune the network timeouts to be more permissive and let a large, distributed cluster of servers work properly.

For more information, [Distributed Architecture](../../distributed/Distributed-Architecture.md).

