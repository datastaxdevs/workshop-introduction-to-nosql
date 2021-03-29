## 🎓🔥 STARGATE, a gateway for multi models data API 🔥🎓

[![License Apache2](https://img.shields.io/hexpm/l/plug.svg)](http://www.apache.org/licenses/LICENSE-2.0)
[![Discord](https://img.shields.io/discord/685554030159593522)](https://discord.com/widget?id=685554030159593522&theme=dark)

![image](pics/splash.png?raw=true)

This intructions will lead you to step by step operations for the workshop on ASTRA + STARGATE for ApiDays Helsinki

## Table of content

1. [Create Astra Instance](#1-create-astra-instance)
2. [Working with Cassandra](#2-working-with-cassandra)
3. [Working with REST API](#3-working-with-rest-api)
4. [Working with DOCUMENT API](#4-working-with-document-api)
5. [Working with GRAPHQL API](#5-working-with-graphql-api)

## 1. Create Astra Instance

**`ASTRA`** is the simplest way to run Cassandra with zero operations at all - just push the button and get your cluster. No credit card required, $25.00 USD credit every month, roughly 5M writes, 30M reads, 40GB storage monthly - sufficient to run small production workloads.  

✅ Register (if needed) and Sign In to Astra [https://astra.datastax.com](https://dtsx.io/workshop): You can use your `Github`, `Google` accounts or register with an `email`.

_Make sure to chose a password with minimum 8 characters, containing upper and lowercase letters, at least one number and special character_

✅ Create a "pay as you go" plan

Follow this [guide](https://docs.datastax.com/en/astra/docs/creating-your-astra-database.html), to set up a pay as you go database with a free $25 monthly credit.

- **Select the pay as you go option**: Includes $25 monthly credit - no credit card needed to set up.

You will find below which values to enter for each field.

- **For the database name** - `free_db.` While Astra allows you to fill in these fields with values of your own choosing, please follow our recommendations to ensure the application runs properly.

- **For the keyspace name** - `keyspace1`. It's really important that you use the name "free" for the code to work.

_You can technically use whatever you want and update the code to reflect the keyspace. This is really to get you on a happy path for the first run._

- **For provider and region**: Choose and provider (either GCP or AWS). Region is where your database will reside physically (choose one close to you or your users).

- **Create the database**. Review all the fields to make sure they are as shown, and click the `Create Database` button.

You will see your new database `pending` in the Dashboard.

![my-pic](https://github.com/datastaxdevs/shared-assets/blob/master/astra/dashboard-pending-1000-update.png?raw=true)

The status will change to `Active` when the database is ready, this will only take 2-3 minutes. You will also receive an email when it is ready.

## 2. Working with Cassandra

**✅ Check that our keyspace exist**

```sql
describe keyspaces;
```

**✅ Create Entities**

```sql
use keyspace1;

CREATE TYPE IF NOT EXISTS video_format (
  width   int,
  height  int
);

CREATE TABLE IF NOT EXISTS videos (
 videoid   uuid,
 title     text,
 upload    timestamp,
 email     text,
 url       text,
 tags      set <text>,
 frames    list<int>,
 formats   map <text,frozen<video_format>>,
 PRIMARY KEY (videoid)
);

describe keyspace1;
```

**✅ Use the data model** :

- Insert value using plain CQL

```sql
INSERT INTO videos(videoid, email, title, upload, url, tags, frames, formats)
VALUES(uuid(), 'clu@sample.com', 'sample video', 
     toTimeStamp(now()), 'http://google.fr',
     { 'cassandra','accelerate','2020'},
     [ 1, 2, 3, 4], 
     { 'mp4':{width:1,height:1},'ogg':{width:1,height:1}});
     
INSERT INTO videos(videoid, email, title, upload, url)
VALUES(uuid(), 'clu@sample.com', 'video2', toTimeStamp(now()), 'http://google.fr');
```

- Insert Value using JSON

```sql
INSERT INTO videos JSON '{
   "videoid":"e466f561-4ea4-4eb7-8dcc-126e0fbfd573",
     "email":"clunven@sample.com",
     "title":"A Second videos",
     "upload":"2020-02-26 15:09:22 +00:00",
     "url": "http://google.fr",
     "frames": [1,2,3,4],
     "tags":   [ "cassandra","accelerate", "2020"],
     "formats": { 
        "mp4": {"width":1,"height":1},
        "ogg": {"width":1,"height":1}
     }
}';
```

- Read values

```sql
select * from videos;
```

- Read by id
```sql
select * from videos where videoid=e466f561-4ea4-4eb7-8dcc-126e0fbfd573;
```

[🏠 Back to Table of Contents](#table-of-content)

## 3. Working with REST API

To use the API we will need a token please create a token following the instructions here:

✅ [Create a token for your app](https://docs.datastax.com/en/astra/docs/manage-application-tokens.html) to use in the settings screen

Copy the token value (eg `AstraCS:KDfdKeNREyWQvDpDrBqwBsUB:ec80667c....`) in your clipboard and save the CSV this value would not be provided afterward.

**👁️ Expected output**
![image](pics/astra-token.png?raw=true)

Now launch the swagger UI
![image](pics/launch-swagger.png?raw=true)


**✅ List keyspaces** : 

Locate the `SCHEMAS` part of the API

![image](pics/swagger-schemas.png?raw=true)

 Local `listAllKeyspaces`

- Click `Try it out`
- Provide your token in the field `X-Cassandra-Token`
- Click on `Execute`

**✅ Creating a keyspace2** : 

- [createKeyspace]
- Data
```json
{"name": "keyspace2","replicas": 3}
```

**✅ Creating a Table** : 

- [addTable]
- X-Cassandra-Token: `<your_token>`
- keyspace: `keyspace2`
- Data
```json
{
  "name": "users",
  "columnDefinitions":
    [
        {
        "name": "firstname",
        "typeDefinition": "text"
      },
        {
        "name": "lastname",
        "typeDefinition": "text"
      },
      {
        "name": "email",
        "typeDefinition": "text"
      },
        {
        "name": "color",
        "typeDefinition": "text"
      }
    ],
  "primaryKey":
    {
      "partitionKey": ["firstname"],
      "clusteringKey": ["lastname"]
    },
  "tableOptions":
    {
      "defaultTimeToLive": 0,
      "clusteringExpression":
        [{ "column": "lastname", "order": "ASC" }]
    }
}
```

Now Locate the `DATA` part of the API

**👁️ Expected output**
![image](pics/astra-data.png?raw=true)

**✅ Insert a row** : 

- [createRow]
- X-Cassandra-Token: `<your_token>`
- keyspace: `keyspace2`
- table: `users`
- Data
```json
{   
 "columns":[
    {"name":"firstname","value":"Mookie"},
    {"name":"lastname","value":"Betts"},
    {"name":"email","value":"mookie.betts@gmail.com"},
    {"name":"color","value":"blue"}
 ]
}
```

**✅ Read data** : 

- [getAllRows]
- X-Cassandra-Token: `<your_token>`
- keyspace: `keyspace2`
- table: `users`

[🏠 Back to Table of Contents](#table-of-content)

## 4. Working with DOCUMENT API

This walkthrough has been realized using the [Quick Start](https://stargate.io/docs/stargate/1.0/quickstart/quick_start-document.html)

locate the Document part in the Swagger UI

![image](pics/swagger-docs.png?raw=true)


**✅ Creating a namespace** :

- Access [createNamespace]
- Fill with Header `X-Cassandra-Token` with `<your_token>`
- Use this payload as JSON
```json
{ "name": "namespace1", "replicas": 3 }
```

**✅ Checking namespace existence** :

- Access [getAllNamespaces]
- Fill with Header `X-Cassandra-Token` with `<your_token>`
- For `raw` you can use either `true` or `false`

**👁️ Expected output**
```json
{
  "data": [
    { "name": "system_distributed" },
    { "name": "system" },
    { "name": "data_endpoint_auth"},
    { "name": "keyspace1" },
    { "name": "namespace1"},
    { "name": "system_schema"},
    { "name": "keyspace2" },
    { "name": "stargate_system"},
    { "name": "system_auth" },
    { "name": "system_traces"}
  ]
}
```

**✅ Create a document** :

```json
{
   "videoid":"e466f561-4ea4-4eb7-8dcc-126e0fbfd573",
     "email":"clunven@sample.com",
     "title":"A Second videos",
     "upload":"2020-02-26 15:09:22 +00:00",
     "url": "http://google.fr",
     "frames": [1,2,3,4],
     "tags":   [ "cassandra","accelerate", "2020"],
     "formats": { 
        "mp4": {"width":1,"height":1},
        "ogg": {"width":1,"height":1}
     }
}
```

**👁️ Expected output**:
```json
{
  "documentId":"5d746e40-97cf-490b-ab0d-68cfbc5d2ef3"
}
```

**✅ Retrieve documents** :

```bash
curl --location \
--request GET 'localhost:8082/v2/namespaces/namespace1/collections/videos?page-size=3' \
--header "X-Cassandra-Token: $AUTH_TOKEN" \
--header 'Content-Type: application/json'
```

**👁️ Expected output**:
```json
{
  "data":{
    "5d746e40-97cf-490b-ab0d-68cfbc5d2ef3":{
      "email":"clunven@sample.com",
      "formats":{"mp4":{"height":1,"width":1},"ogg":{"height":1,"width":1}},"frames":[1,2,3,4],
      "tags":["cassandra","accelerate","2020"],"title":"A Second videos","upload":"2020-02-26 15:09:22 +00:00","url":"http://google.fr","videoid":"e466f561-4ea4-4eb7-8dcc-126e0fbfd573"
     }
   }
}
```

**✅ Retrieve 1 document** :

```bash
curl -L \
-X GET 'localhost:8082/v2/namespaces/namespace1/collections/videos/5d746e40-97cf-490b-ab0d-68cfbc5d2ef3' \
--header "X-Cassandra-Token: $AUTH_TOKEN" \
--header 'Content-Type: application/json'
```

**👁️ Expected output**:
```json
{
  "documentId":"5d746e40-97cf-490b-ab0d-68cfbc5d2ef3",
  "data":{
     "email":"clunven@sample.com",
     "formats":{"mp4":{"height":1,"width":1},"ogg":{"height":1,"width":1}},
     "frames":[1,2,3,4],
     "tags":["cassandra","accelerate","2020"],
     "title":"A Second videos",
     "upload":"2020-02-26 15:09:22 +00:00",
     "url":"http://google.fr",
     "videoid":"e466f561-4ea4-4eb7-8dcc-126e0fbfd573"
   }
}
```

**✅ Search for document by properties** :

```JSON
{"email":
   { "$eq":"clunven@sample.com" }
} 
```

**👁️ Expected output**:
```json
{"data":{
   "5d746e40-97cf-490b-ab0d-68cfbc5d2ef3":{
      "email":"clunven@sample.com",
      "formats":{"mp4":{"height":1,"width":1},"ogg":{"height":1,"width":1}},
      "frames":[1,2,3,4],
      "tags":["cassandra","accelerate","2020"],
      "title":"A Second videos",
      "upload":"2020-02-26 15:09:22 +00:00",
      "url":"http://google.fr",
      "videoid":"e466f561-4ea4-4eb7-8dcc-126e0fbfd573"
    }
  }
}
```

[🏠 Back to Table of Contents](#table-of-content)

## 5. Working with GRAPHQL API

This walkthrough has been realized using the [GraphQL Quick Start](https://stargate.io/docs/stargate/1.0/quickstart/quick_start-graphql.html)

**✅ Open GraphQL Playground** :

Open the playground
![image](pics/launch-graphql.png?raw=true)

**👁️ Expected output**
![image](pics/playground-home.png?raw=true)

**✅ Creating a keyspace** :

Before you can start using the GraphQL API, you must first create a Cassandra keyspace and at least one table in your database. If you are connecting to a Cassandra database with existing schema, you can skip this step.

Inside the GraphQL playground, navigate to http://localhost:8080/graphql-schema and create a keyspace by executing the following mutation:

```
mutation createKeyspaceLibrary {
  createKeyspace(name:"library", replicas: 1)
}
```

Add the auth token to the HTTP Headers box in the lower lefthand corner:
```
{
  "x-cassandra-token":"7c37bda5-7360-4d39-96bc-9765db5773bc"
}
```

**👁️ Expected output**

![image](pics/graphql-createkeyspace.png?raw=true)

**✅ Creating a Table** :

- Use this query
```
mutation {
  books: createTable(
    keyspaceName:"library",
    tableName:"books",
    partitionKeys: [ # The keys required to access your data
      { name: "title", type: {basic: TEXT} }
    ]
    values: [ # The values associated with the keys
      { name: "author", type: {basic: TEXT} }
    ]
  )
  authors: createTable(
    keyspaceName:"library",
    tableName:"authors",
    partitionKeys: [
      { name: "name", type: {basic: TEXT} }
    ]
    clusteringKeys: [ # Secondary key used to access values within the partition
      { name: "title", type: {basic: TEXT}, order: "ASC" }
    ]
  )
}
```

**👁️ Expected output**

![image](pics/graphql-createtables.png?raw=true)

**✅ Populating Table** :

Any of the created APIs can be used to interact with the GraphQL data, to write or read data.

First, let’s navigate to your new keyspace `library` inside the playground. Change tab to `graphql` and pick url `/graphql/library`.

- Use this query
```
mutation insert2Books {
  moby: insertbooks(value: {title:"Moby Dick", author:"Herman Melville"}) {
    value {
      title
    }
  }
  catch22: insertbooks(value: {title:"Catch-22", author:"Joseph Heller"}) {
    value {
      title
    }
  }
}
```

- Don't forget to update the header again
```
{
  "x-cassandra-token":"7c37bda5-7360-4d39-96bc-9765db5773bc"
}
```
**👁️ Expected output**

![image](pics/graphql-insertdata.png?raw=true)


**✅ Read data** :

Stay on the same screen and sinmply update the query with 
```
query oneBook {
    books (value: {title:"Moby Dick"}) {
      values {
        title
        author
      }
    }
}
```

**👁️ Expected output**

![image](pics/graphql-readdata.png?raw=true)

[🏠 Back to Table of Contents](#table-of-content)


## THE END

Congratulation your made it to the END.


