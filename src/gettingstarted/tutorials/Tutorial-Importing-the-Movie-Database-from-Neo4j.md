---
search:
   keywords: ['import', 'Neo4j', 'migration', 'GraphML', 'movie', 'cypher', 'tutorial']
---

| | |
|----|-----|
|![](../../images/warning.png)| This is a legacy strategy to migrate from Neo4j. The new strategy is to migrate using the [Neo4j to OrientDB Importer](../../neo4j-to-orientdb-importer/README.md). 


# Tutorial: Importing the *movie* Database from Neo4j

In this tutorial we will follow the steps described in the [Import from Neo4j using GraphML](../../admin/Import-from-Neo4j-using-GraphML.md) section to import the Neo4j's *movie* example database into OrientDB.

We will also provide some examples of queries using the OrientDB's [MATCH](../../sql/SQL-Match.md) syntax, making a comparison with the corresponding Neo4j's Cypher query language.

For general information on the possible Neo4j to OrientDB migration strategies, please refer to the [Import from Neo4j](../../admin/Import-from-Neo4j-into-OrientDB.md) section. 

>Neo4j and Cypher are registered trademark of Neo Technology, Inc.

## Exporting from Neo4j

Assuming you have already downloaded and unpacked the [Neo4j Shell Tools](https://github.com/jexp/neo4j-shell-tools), and restarted the Neo4j Server, as described in the Section [Exporting GraphML](../../admin/Import-from-Neo4j-using-GraphML.md#exporting-graphml), you can export the *movie* database using `neo4j-shell` with a command like the following one:

```
D:\neo4j\neo4j-community-3.0.6\bin>neo4j-shell.bat

Welcome to the Neo4j Shell! Enter 'help' for a list of commands
NOTE: Remote Neo4j graph database service 'shell' at port 1337

neo4j-sh (?)$ export-graphml -t -o d:/movie.graphml
Wrote to GraphML-file d:/movies.graphml 0. 100%: nodes = 171 rels = 253 properties = 564 time 270 ms total 270 ms
```

In the example above the exported *movie* graph is stored under `D:\movie.graphml`.


## Importing into OrientDB

In this tutorial we will import in OrientDB the file `movie.graphml` using the OrientDB's [Console](../../console/README.md). For other GraphML import methods, please refer to the section [Importing GraphML](../../admin/Import-from-Neo4j-using-GraphML.md#importing-graphml).

The OrientDB's Console output generated during the import process is similar to the following (note that first we create a *movie* database using the command `CREATE DATABASE`, and then we do the actual import using the command `IMPORT DATABASE`):

```
D:\orientdb\orientdb-enterprise-2.2.8\bin>console.bat

OrientDB console v.2.2.8-SNAPSHOT (build 2.2.x@r39259e190e16045fe1425b1c0485f8562fca055b; 2016-08-23 14:38:49+0000) www.orientdb.com
Type 'help' to display all the supported commands.
Installing extensions for GREMLIN language v.2.6.0

orientdb> CREATE DATABASE PLOCAL:D:/orientdb/orientdb-enterprise-2.2.8/databases/movie

Creating database [PLOCAL:D:/orientdb/orientdb-enterprise-2.2.8/databases/movie] using the storage type [PLOCAL]...
Database created successfully.

Current database is: PLOCAL:D:/orientdb/orientdb-enterprise-2.2.8/databases/movie
orientdb {db=movie}> IMPORT DATABASE D:/movie.graphml

Importing GRAPHML database from D:/movie.graphml with options ()...
Done: imported 171 vertices and 253 edges
orientdb {db=movie}>
```

As you can see from the output above, as a result of the import 171 vertices and 253 edges have been created in OrientDB. This is exactly the same number of nodes and relationships exported from Neo4j.

For more tips and tricks related to the import process, please refer to [this](../../admin/Import-from-Neo4j-using-GraphML.md#import-tips-and-tricks) section.


## Query Comparison

Once the *movie* database has been imported into OrientDB, you may use several ways to access its data.

The `MATCH` [syntax](../../sql/SQL-Match.md) and the tool [Studio](../../studio/README.md) can be used, for instance, in a similar way to the Neo4j's Cypher and Browser.

The following sections include a comparison of the Neo4j's Cypher and OrientDB's `MATCH` syntax for some queries that you can execute against the *movie* database.


### Find the actor named "Tom Hanks"

Neo4j's Cypher:

```
MATCH (tom:Person {name: "Tom Hanks"}) 
RETURN tom
```

OrientDB's MATCH:

```
MATCH {class: Person, as: tom, where: (name = 'Tom Hanks')} 
RETURN $pathElements
```

### Find the movie with title "Cloud Atlas"

Neo4j's Cypher:

```
MATCH (cloudAtlas:Movie {title: "Cloud Atlas"}) 
RETURN cloudAtlas
```

OrientDB's MATCH:

```
MATCH {class: Movie, as: cloudAtlas, where: (title = 'Cloud Atlas')} 
RETURN $pathElements
```

### Find 10 people

Neo4j's Cypher:

```
MATCH (people:Person) 
RETURN people.name 
LIMIT 10
```

OrientDB's MATCH:

```
MATCH {class: Person, as: people} 
RETURN people.name
LIMIT 10
``` 

### Find the movies released in the 1990s

Neo4j's Cypher:

```
MATCH (nineties:Movie) 
WHERE nineties.released > 1990 AND nineties.released < 2000 
RETURN nineties.title
```

OrientDB's MATCH:

```
MATCH {class: Movie, as: nineties, WHERE: (released > 1990 AND released < 2000 )} 
RETURN nineties.title
```

### List all Tom Hanks movies

Neo4j's Cypher:

```
MATCH (tom:Person {name: "Tom Hanks"})-[:ACTED_IN]->(tomHanksMovies) 
RETURN tom, tomHanksMovies
```

OrientDB's MATCH:

```
MATCH {class: Person, as: tom, where: (name = 'Tom Hanks')}-ACTED_IN->{as: tomHanksMovies}
RETURN $pathElements
```

### Find out who directed "Cloud Atlas"

Neo4j's Cypher:

```
MATCH (cloudAtlas {title: "Cloud Atlas"})<-[:DIRECTED]-(directors)
RETURN directors.name
```

OrientDB's MATCH:

```
MATCH {class: Movie, as: cloudAtlas, where: (title = 'Cloud Atlas')}<-DIRECTED-{as: directors}
RETURN directors.name
```

### Find Tom Hanks' co-actors

Neo4j's Cypher:

```
MATCH (tom:Person {name:"Tom Hanks"})-[:ACTED_IN]->(m)<-[:ACTED_IN]-(coActors) 
RETURN DISTINCT coActors.name
```

OrientDB's MATCH:

```
MATCH {class: Person, as: tom, where: (name = 'Tom Hanks')}-ACTED_IN->{as: m}<-ACTED_IN-{class: Person,as: coActors}
RETURN coActors.name
```

### Find how people are related to "Cloud Atlas"

Neo4j's Cypher:

```
MATCH (people:Person)-[relatedTo]-(:Movie {title: "Cloud Atlas"}) 
RETURN people.name, Type(relatedTo), relatedTo
```

OrientDB's MATCH:

```
MATCH {class: Person, as: people}--{as: m, where: (title = 'Cloud Atlas')}
RETURN $pathElements
```
