## 🎓🔥 Introduction to NotOnly SQL Databases

[![License Apache2](https://img.shields.io/hexpm/l/plug.svg)](http://www.apache.org/licenses/LICENSE-2.0)
[![Discord](https://img.shields.io/discord/685554030159593522)](https://discord.com/widget?id=685554030159593522&theme=dark)

![image](images/splash.jpeg?raw=true)

This intructions will lead you to step by step operations for the workshop introducing the NoSQL Databases technologies. 

## Materials for the Session

It doesn't matter if you join our workshop live or you prefer to do at your own pace, we have you covered. In this repository, you'll find everything you need for this workshop:

- [Workshop video](#)
- [Slide deck](./slides.pdf)
- [Discord chat](https://bit.ly/cassandra-workshop)
- [Questions and Answers](https://community.datastax.com/)

## Partitipation Certificate / Homework

To get the participation certificate, you have to complete following steps:

1. Complete the practice steps of this workshop as explained below, steps I-IV are mandatory, step V is optional. Take screenshots of the last completed step from either step IV or V. _NOTE: When taking screenshots ensure NOT to copy your Astra DB key!_
2. Complete [try-it-out scenario](https://www.datastax.com/try-it-out) and make a screenshot of the "scenario completed" screen
3. Submit the practice [here](https://github.com/datastaxdevs/workshop-introduction-to-nosql/issues/new?assignees=clun&labels=wait+for+review%2C+homework&template=homework-assignment.md&title=%5BHW%5D+%3CNAME%3E) attaching the screenshots.

## Practice

1. [Create Astra Db Instance](#1-create-astra-db-instance)
2. [Tabular Databases](#2-tabular-databases)
3. [Document Databases](#3-document-databases)
4. [Key-Value Databases](#4-keyvalue-databases)
5. [Graph Databases](#5-graph-databases)

## 1. Create Astra Db Instance

**`ASTRA DB`** is the simplest way to run Cassandra with zero operations at all - just push the button and get your cluster. No credit card required, $25.00 USD credit every month, roughly 5M writes, 30M reads, 40GB storage monthly - sufficient to run small production workloads.

✅ Register (if needed) and Sign In to Astra DB [https://astra.datastax.com](http://dtsx.io/astraDB-6-16): You can use your `Github`, `Google` accounts or register with an `email`.

_Make sure to chose a password with minimum 8 characters, containing upper and lowercase letters, at least one number and special character_

✅ Create a "pay as you go" plan

Follow this [guide](https://docs.datastax.com/en/astra/docs/creating-your-astra-database.html), to set up a pay as you go database with a free $25 monthly credit.

- **Select the pay as you go option**: Includes $25 monthly credit - no credit card needed to set up.

You will find below which values to enter for each field.

- **For the database name** - `nosql_db`. While Astra DB allows you to fill in these fields with values of your own choosing, please follow our recommendations to ensure the application runs properly.

- **For the keyspace name** - `nosql1`. It's really important that you use the name "nosql1" for the code to work.

_You can technically use whatever you want and update the code to reflect the keyspace. This is really to get you on a happy path for the first run._

- **For provider and region**: Choose and provider (either GCP or AWS). Region is where your database will reside physically (choose one close to you or your users).

- **Create the database**. Review all the fields to make sure they are as shown, and click the `Create Database` button.

You will see your new database `pending` in the Dashboard.

![my-pic](https://github.com/datastaxdevs/shared-assets/blob/master/astra/dashboard-pending-1000-update.png?raw=true)

The status will change to `Active` when the database is ready, this will only take 2-3 minutes. You will also receive an email when it is ready.

## 2. Tabular databases

In a tabular database we will store ... tables ! The Astra DB Service is based on Apache Cassandra which is tabular it make sense to start by this one.

> **Tabular databases** organize data in rows and columns, but with a twist from the traditional RDBMS. Also known as wide-column stores or partitioned row stores, they provide the option to organize related rows in partitions that are stored together on the same replicas to allow fast queries. Unlike RDBMSs, the tabular format is not necessarily strict. For example, Apache Cassandra™ does not require all rows to contain values for all columns in the table. Like Key/Value and Document databases, Tabular databases use hashing to retrieve rows from the table. Examples include: Cassandra, HBase, and Google Bigtable.

**✅ 2a. Describe your Keyspace**

At Database creation you provided a keyspace, a logical grouping for tables let's visualize it. In Astra DB go to CQL Console to enter the following commands

- *Select your db*
![image](images/01.png?raw=true)

- *Select CqlConsole*
![image](images/02.png?raw=true)

- *Enter the command*
```sql
describe keyspaces;
```

![image](images/03.png?raw=true)

**✅ 2b. Create tables**

- *Execute the following Cassandra Query Language* 

```sql
use nosql1;

CREATE TABLE IF NOT EXISTS videos (
 videoid   uuid,
 email     text,
 title     text,
 upload    timestamp,
 url       text,
 tags      set <text>,
 frames    list<int>,
 PRIMARY KEY (videoid)
);
```

- *Visualize structure*
```sql
describe keyspace nosql1;
```

**✅ 2c. Working with DATA** :

- *Insert some entries on first table*
```sql
INSERT INTO videos(videoid, email, title, upload, url, tags, frames)
VALUES(uuid(), 
     'clu@sample.com', 
     'Introduction to Nosql Databases', 
     toTimeStamp(now()), 
     'https://www.youtube.com/watch?v=cMDHxsGbmC8',
     { 'nosql','workshop','2021'}, 
     [ 1, 2, 3, 4]);
     
INSERT INTO videos(videoid, email, title, upload, url)
VALUES(uuid(), 
      'clu@sample.com', 
      'Demo video Y', 
      toTimeStamp(now()), 
      'https://www.youtube.com/watch?v=XXXX');

INSERT INTO videos(videoid, email, title, upload, url)
VALUES(e466f561-4ea4-4eb7-8dcc-126e0fbfd573, 
      'clu@sample.com', 
      'Yet another video', 
      toTimeStamp(now()), 
      'https://www.youtube.com/watch?v=ABCDE');
```

- *Read values*

```sql
select * from videos;
```

- *Read by id*

```sql
select * from videos 
where videoid=e466f561-4ea4-4eb7-8dcc-126e0fbfd573;
```

**✅ 2d. Working with PARTITIONS** :

But data can be grouped, we stored together what should be retrieved together.

- *Create This new table*
```sql
CREATE TABLE IF NOT EXISTS users_by_city (
 city      text,
 firstname text,
 lastname  text,
 email     text,
 PRIMARY KEY ((city), lastname, email)
) WITH CLUSTERING ORDER BY(lastname ASC, email ASC);

```
- *Insert some entries*

```sql
INSERT INTO users_by_city(city, firstname, lastname, email) 
VALUES('PARIS', 'Lunven', 'Cedrick', 'clu@sample.com');

INSERT INTO users_by_city(city, firstname, lastname, email) 
VALUES('PARIS', 'Yellow', 'Jackets', 'yj@sample.com');

INSERT INTO users_by_city(city, firstname, lastname, email) 
VALUES('ORLANDO', 'David', 'Gilardi', 'dgi@sample.com');
```

- *List values with partitions keys*

```sql
SELECT * from users_by_city WHERE city='PARIS';
```

- *List values without parititions keys*
```sql
SELECT * from users_by_city WHERE lastname='Gilardi';
```

**`Yes we know`** and we will explain why.

- *Same query with debugging enabled*

```sql
tracing on;
SELECT * from users_by_city WHERE city='PARIS';
```

- *Forcing Full Scan = SLOW*
```sql
SELECT * from users_by_city WHERE lastname='Gilardi' ALLOW FILTERING;
```

- *Stop debugging*
```sql
tracing off;
```

[🏠 Back to Table of Contents](#table-of-content)

## 3. Document Databases

Let's do some hands-on with document database queries.

> **Document databases** expand on the basic idea of key-value stores where “documents” are more complex, in that they contain data and each document is assigned a unique key, which is used to retrieve the document. These are designed for storing, retrieving, and managing document-oriented information, often stored as JSON. Since the Document database can inspect the document contents, the database can perform some additional retrieval processing. Unlike RDBMSs which require a static schema, Document databases have a flexible schema as defined by the document contents. Examples include: MongoDB and CouchDB. Note that some RDBMS and NoSQL databases outside of pure document stores are able to store and query JSON documents, including Cassandra.

**✅ 3a. Cassandra knows JSON** :

It is not a known fact but Cassandra accepts JSON query out of the box. You can find more information [here](https://docs.datastax.com/en/cql-oss/3.3/cql/cql_using/useInsertJSON.html)

- *Insert data into Cassandra with JSON*

```sql
INSERT INTO videos JSON '{
   "videoid":"e466f561-4ea4-4eb7-8dcc-126e0fbfd578",
     "email":"clunven@sample.com",
     "title":"A JSON videos",
     "upload":"2020-02-26 15:09:22 +00:00",
     "url": "http://google.fr",
     "frames": [1,2,3,4],
     "tags":   [ "cassandra","accelerate", "2020"]
}';
```

In the same way you can retrieve JSON out of Cassandra ([more info here](https://docs.datastax.com/en/cql-oss/3.3/cql/cql_using/useQueryJSON.html))

- *Retrieve data from Cassandra as JSON*

```sql
select json title,url,tags from videos;
```

![image](images/04.png?raw=true)

**✅ 3b. Create your Application token** :

Use this [documentation](https://docs.datastax.com/en/astra/docs/manage-application-tokens.html) to create your application token. Copy the token value in a text file somewhere we will reuse it a lot 

*Astra DB Token format (do not copy)*
```
AstraCS:KDfdKeNREyWQvDpDrBqwBsUB:ec80667c....
```

![image](images/token.png?raw=true)

This walkthrough has been realized using the [Quick Start](https://stargate.io/docs/stargate/1.0/quickstart/quick_start-document.html)

Next go the connect page, locate the SWAGGER URL

![image](images/connect.png?raw=true)

Locate the Document part in the Swagger UI.

![image](images/05.png?raw=true)

**✅ 3c Create a new empty collection** :

- Access ***Create a new empty collection in a namespace***
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `body` use 

```json
{ "name": "col1" }
```
- Click `Execute` button

You will get a 201 returned code

**✅ 3d. Create a new document** :

- Access ***Create a new document***
- Click `Try it out` button
- Fill with Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `collection-id` use `col1`
- For `body` use 

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
- Click `Execute` button

*👁️ Expected output*
```json
{
  "documentId":"5d746e40-97cf-490b-ab0d-68cfbc5d2ef3"
}
```

You can add a couple of documents changing values, new documents with new ids will be generated

**✅ 3e Find all documents of a collection** :

- Access ***Search documents in a collection***
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `collection-id` use `col1`

Let other fields blank every query is paged in Cassandra.

**👁️ Expected output**:
```json
{
  "data": {
    "84bd6ebc-a274-4dc3-ae7c-eb2fd913331b": {
      "email": "clunven@sample.com",
      "formats": {
        "mp4": {
          "height": 1,
          "width": 1
        },
        "ogg": {
          "height": 1,
          "width": 1
        }
      },
      "frames": [
        1,
        2,
        3,
        4
        ...
```
- Click `Execute` button

**✅ 3f Retrieve a document from its id** :

- Access ***Get a document***
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `collection-id` use `col1`
- For `document-id` use `<doc_id_in_previous_call>`
- Click `Execute` button

**👁️ Expected output**:
```json
{
  "documentId": "84bd6ebc-a274-4dc3-ae7c-eb2fd913331b",
  "data": {
    "email": "clunven@sample.com",
    "formats": {
      "mp4": {
        "height": 1,
        "width": 1
      },
      "ogg": {
        "height": 1,
        "width": 1
      }
    },
    "frames": [
      1,
      2,
      3,
      4
    ],
    "tags": [
      "cassandra",
      "accelerate",
      "2020"
    ],
    "title": "A Second videos",
    "upload": "2020-02-26 15:09:22 +00:00",
    "url": "http://google.fr",
    "videoid": "e466f561-4ea4-4eb7-8dcc-126e0fbfd573"
  }
}
```

**✅ 3g Search document from a where clause** :

- Access ***Search documents in a collection***
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `collection-id` use `col1`
- For `where` use `{"email":    { "$eq":"clunven@sample.com" } } `
- Click `Execute` button

*👁️ Expected output*
```json
{
  "data": {
    "84bd6ebc-a274-4dc3-ae7c-eb2fd913331b": {
      "email": "clunven@sample.com",
      "formats": {
        "mp4": {
          "height": 1,
          "width": 1
        },
        "ogg": {
          "height": 1,
          "width": 1
        }
      },
      "frames": [
        1,
        2,
        3,
        4
      ],
      "tags": [
        "cassandra",
        "accelerate",
        "2020"
      ],
      "title": "A Second videos",
      "upload": "2020-02-26 15:09:22 +00:00",
      "url": "http://google.fr",
      "videoid": "e466f561-4ea4-4eb7-8dcc-126e0fbfd573"
    }
  }
}
```

[🏠 Back to Table of Contents](#table-of-content)

## 4. KeyValue Databases

> **Key/Value databases** are some of the least complex as all of the data within consists of an indexed key and a value. Key-value databases use a hashing mechanism such that given a key, the database can quickly retrieve an associated value. Hashing mechanisms provide constant time access, which means they maintain high performance even at large scale. The keys can be any type of object, but are typically a string. The values are generally opaque blobs (i.e., a sequence of bytes that the database does not interpret). Examples include: Redis, Amazon DynamoDB, Riak, and Oracle NoSQL database. Some tabular NoSQL databases, like Cassandra, can also service key/value needs.

**✅ 4b. Create a table with GraphQL**

Navigate to the GraphQL playground, by clicking `Connect`, then `GraphQL API`, and finally you should see the URL under `Launching GraphQL Playground`. Ctrl-click or right-click the link to launch into a new tab.

![Screen Shot 2021-05-19 at 11 19 11 AM](https://user-images.githubusercontent.com/23346205/118839150-452a4200-b894-11eb-8320-20b15f3b1c09.png)

Then you need to enter in your Astra DB token to authenticate and talk to the database. Notice the "server cannot be reached" message. This is simply telling you it cannot make the connection because you are not authenticated. Click on `HTTP Headers` at the bottom and paste in your token.

![Screen Shot 2021-05-20 at 8 49 48 AM](https://user-images.githubusercontent.com/23346205/118981634-88de8380-b948-11eb-9ece-5f75f153020e.png)

Paste in your token.

![Screen Shot 2021-05-20 at 8 53 07 AM](https://user-images.githubusercontent.com/23346205/118982115-18843200-b949-11eb-840c-acce0a0c562a.png)

Now you are ready to go.

Use this query. Since we are creating a table we want to use the `graphql-schema` tab
```json
mutation {
  kv: createTable(
    keyspaceName:"nosql1",
    tableName:"key_value",
    partitionKeys: [ # The keys required to access your data
      { name: "key", type: {basic: TEXT} }
    ]
    values: [ # The values associated with the keys
      { name: "value", type: {basic: TEXT} }
    ]
  )
}
```

![Screen Shot 2021-05-20 at 8 58 13 AM](https://user-images.githubusercontent.com/23346205/118982905-df988d00-b949-11eb-8584-9407c9efa80e.png)

You can check in the CQL Cosole as well;

```sql
use nosql1;

describe table key_value;
```

*Expected output*

![image](images/cqlconsole1.png?raw=true)

**✅ 4c. Populate the table**

Any of the created APIs can be used to interact with the GraphQL data, to write or read data. Since we are working with data now and not creating schema you will need to swtich to the `graphql` tab. Also, ensure to fill in your Astra DB token again.

- *Fill the header token again*
```json
{
  "x-cassandra-token":"AstraCS:fjlsgehrre;ge"
}
```

![Screen Shot 2021-05-20 at 9 04 47 AM](https://user-images.githubusercontent.com/23346205/118983681-af9db980-b94a-11eb-82b7-e0c852701265.png)

Now, let’s navigate to your new keyspace `nosql1` inside the playground. Change tab to graphql and pick url `/graphql/nosql1`

![Screen Shot 2021-05-20 at 9 13 26 AM](https://user-images.githubusercontent.com/23346205/118985049-1d96b080-b94c-11eb-87c4-0340e941d37c.png)


Then paste in the following query and click "play". 
![Screen Shot 2021-05-20 at 9 17 35 AM](https://user-images.githubusercontent.com/23346205/118985407-6c444a80-b94c-11eb-9b7d-d83e35e40bf1.png)

- *Execute this query*
```json
mutation insert2KV {
  key1: insertkey_value(value: {key:"key1", value:"bbbb"}) {
    value {
      key,value
    }
  }
  key2: insertkey_value(value: {key:"key2", value:"bbbb"}) {
    value {
      key,value
    }
  }
}
```

- Check with CQL CConsole

```sql
select * from key_value;
```

*Expected output:*

![image](images/graphql2.png?raw=true)

- *Execute this query*
```json
mutation insert2KV {
  key1: insertkey_value(value: {key:"key1", value:"cccc"}) {
    value {
      key,value
    }
  }
}
```

- Check with CQL Console the values should have been updated. Indeed with Cassandra no integrite constraints so you can de-duplicate values easily,

[🏠 Back to Table of Contents](#table-of-content)

## 5. Graph Databases

> **Graph databases** store their data using a graph metaphor to exploit the relationships between data. Nodes in the graph represent data items, and edges represent the relationships between the data items. Graph databases are designed for highly complex and connected data, which outpaces the relationship and JOIN capabilities of an RDBMS. Graph databases are often exceptionally good at finding commonalities and anomalies among large data sets. Examples of Graph databases include DataStax Graph, Neo4J, JanusGraph, and Amazon Neptune.

Astra DB does not contain yet a way to implement Graph Databases use cases. But at Datastax Companny we do have a product called [DataStax Graph](https://www.datastax.com/products/datastax-graph) that you can use for free when not in production.

Today it will be a demo to be quick but you can as well do and start the demo with the following steps

**✅ 5a. Prerequisites**

**Minimal Configuration**: You need to have a computer with this minimal configuration requirements
- At least 2CPU
- At least 3GB or RAM

**Install Docker and Docker Compose**

You tneed to install Docker and Docker-compose on your machine
- [Install **Docker** for Windows/Mac/Linux](https://github.com/DataStax-Academy/kubernetes-workshop-online/blob/master/0-setup-your-cluster/README.MD#1-install-docker)
- [Install **Docker-Compose**  for Windows/Mac/Linux](https://github.com/DataStax-Academy/kubernetes-workshop-online/blob/master/0-setup-your-cluster/README.MD#2-install-docker-compose)

**✅ 5b. Create a docker network named 'graph'**

```bash
docker network create graph
```

🖥️ *Expected output*
```bash
$workshop_introduction_to_nosql> docker network create graph

64f8bcc2dda416d6dc80ef3c1ac97902b9d90007842808308e9d741d179d9344
```

**✅ 5c.Clone this repository (or download ZIP from the github UI)**

```bash
git clone https://github.com/datastaxdevs/workshop-introduction-to-nosql.git

cd workshop-introduction-to-nosql
```

**✅ 5d.Start the containers**

:warning: *Linux users:*
  >Folder `datastax-studio-config` is mapped to docker container (see: `docker-compose.yaml` file) and dse studio runs as user `studio` with `uid=997` and         >`gui=997` which needs RW access to that folder.
  >
  >Run this command if you are on a linux system:
  >```bash
  >sudo chown -R 997:997 ./datastax-studio-config
  >```
:warning: *Linux users:*

Start containers:
```bash
docker-compose up -d
```

🖥️ *Expected output*
```bash
$workshop_introduction_to_nosql> docker-compose up -d

Creating dse ... done
Creating workshop-introduction-to-nosql_studio_1 ... done
```
Wait for the application to start (30s) and open [http://localhost:9091](http://localhost:9091)


![image](images/studio_home.png?raw=true)

**✅ 5e.Check database connection**

Open the ellipsis and click `Connections`

![image](images/studio_test_connection1.png?raw=true)

Select the `default localhost` connection

![image](images/studio_test_connection2.png?raw=true)

Check that `dse` is set for the host (pointing to a local cassandra)

![image](images/studio_test_connection3.png?raw=true)

Click the button `Test` and expect the output `Connected Successfully`

![image](images/studio_test_connection4.png?raw=true)

**✅ 5f. Open the notebook Work**

Use the ellipsis to now select `Notebooks`

![image](images/studio_home.png?raw=true)

Once the notebook open it has you to creat the graph click `Create` button

![image](images/studio_create_graph.png?raw=true)

Execute cell after call spotting the `Real Time >` button in each cell (top right) 

![image](images/studio_notebook_1.png?raw=true)

Voila ! 

![image](images/studio_notebook_2.png?raw=true)

**✅ 5g. Close Notebook**

To close open notebooks you can now use

```bash
docker-compose down
```

🖥️ *Expected output*
```bash
$workshop_introduction_to_nosql> docker-compose down
Stopping workshop-introduction-to-nosql_studio_1 ... done
Stopping dse                                     ... 
```

[🏠 Back to Table of Contents](#table-of-content)

## THE END

Congratulation you made it to the END.


