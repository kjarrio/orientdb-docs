
# Key Value Use Case

OrientDB can also be used as a Key Value DBMS by using the super fast [Indexes](../indexing/Indexes.md). You can have as many [Indexes](../indexing/Indexes.md) as you need.

# HTTP
OrientDB RESTful HTTP protocol allows to talk with a OrientDB Server instance using the [HTTP protocol](../misc/OrientDB-REST.md) and JSON. OrientDB supports also a highly optimized Binary protocol for superior performances.

## Operations
To interact against OrientDB indexes use the four methods of the HTTP protocol in REST fashion:

- **PUT**, to create or modify an entry in the database
- **GET**, to retrieve an entry from the database. It's idempotent that means no changes to the database happen. Remember that in IE6 the URL can be maximum of 2,083 characters. Other browsers supports longer URLs, but if you want to stay compatible with all limit to 2,083 characters
- **DELETE**, to delete an entry from the database

## Create an entry ##
To create a new entry in the database use the [Index-PUT API](../misc/OrientDB-REST.md#put---index).

Syntax: ```http://<server>:[<port>]/index/<index-name>/<key>```

Example:

HTTP PUT: ```http://localhost:2480/index/customers/jay```
```json   
{
  "name" : "Jay",
  "surname" : "Miner"
}
```

HTTP Response 204 is returned.

## Retrieve an entry ##
To retrieve an entry from the database use the [Index-GET API](../misc/OrientDB-REST.md#put---index).

Syntax: ```http://<server>:[<port>]/index/<index-name>/<key>```

Example:

HTTP GET: ```http://localhost:2480/index/customers/jay```

HTTP Response 200 is returned with this JSON as payload:

```json   
{
  "name" : "Jay",
  "surname" : "Miner"
}
```
## Remove an entry ##
To remove an entry from the database use the [Index-DELETE API](../misc/OrientDB-REST.md#put---index).

Syntax: ```http://<server>:[<port>]/index/<index-name>/<key>```

Example:

HTTP DELETE: ```http://localhost:2480/index/customers/jay```

HTTP Response 200 is returned

# Step-by-Step tutorial

Before to start assure you've a OrientDB server up and running. In this example we'll use curl considering the connection to localhost to the default HTTP post 2480. The default "admin" user is used.

## Create a new index
To use OrientDB as a Key/Value store we need a brand new manual index, let's call it "mainbucket". We're going to create it as UNIQUE because keys cannot be duplicated. If you can have multiple keys consider:
- creating the index as NOTUNIQUE
- leave it as UNIQUE but as value handle array of documents

Create the new manual unique index "mainbucket":
```sh
> curl --basic -u admin:admin localhost:2480/command/demo/sql -d "create index mainbucket UNIQUE STRING"
```
Response:
```json   
{ "result" : [ 
    { "@type" : "d" , "@version" : 0, "value" : 0, "@fieldTypes" : "value=l" }
  ]
}
```

## Store the first entry
Below we're going to insert the first entry by using the HTTP PUT method passing "jay" as key in the URL and as value the entire document in form of JSON:
```sh
> curl --basic -u admin:admin -X PUT localhost:2480/index/demo/mainbucket/jay -d "{'@class': 'V', 'name':'Jay','surname':'Miner'}"
```
Response:
```
Key 'jay' correctly inserted into the index mainbucket.
```

## Retrieve the entry just inserted
Below we're going to retrieve the entry we just entered by using the HTTP GET method passing "jay" as key in the URL:
```
> curl --basic -u admin:admin localhost:2480/index/demo/mainbucket/jay
```
Response:
```json
[{
  "@type" : "d" , "@rid" : "#3:477" , "@version" : 0,
  "name" : "Jay",
  "surname" : "Miner"
}]
```
Note that an array is always returned in case multiple records are associated to the same key (if NOTUNIQUE index is used). Look also at the document has been created with [RID](../datamodeling/Concepts.md#record-id) #3:477. You can load it directly if you know the [RID](../datamodeling/Concepts.md#record-id). Remember to remove the # character. Example:

```sh
> curl --basic -u admin:admin localhost:2480/document/demo/3:477
```
Response:
```json
{
  "@type" : "d" , "@rid" : "#3:477" , "@version" : 0,
  "name" : "Jay",
  "surname" : "Miner"
}
```

## Drop an index
Once finished drop the index "mainbucket" created for the example:
```sh
> curl --basic -u admin:admin localhost:2480/command/demo/sql -d "drop index mainbucket"
```json
Response:
```json   
{ "result" : [ 
    { "@type" : "d" , "@version" : 0, "value" : 0, "@fieldTypes" : "value=l" }
  ]
}
```
