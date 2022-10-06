## 🎓🔥 Introduction to NoSQL Databases

[![License Apache2](https://img.shields.io/hexpm/l/plug.svg)](http://www.apache.org/licenses/LICENSE-2.0)
[![Discord](https://img.shields.io/discord/685554030159593522)](https://discord.com/widget?id=685554030159593522&theme=dark)

![image](images/intro-to-nosql-cover.png?raw=true)

These instructions will lead you step by step for the workshop on introducing the NoSQL Databases technologies.

## Materials for the Session

It doesn't matter if you join our workshop live or you prefer to do at your own pace, we have you covered. In this repository, you'll find everything you need for this workshop:

- [Workshop video](#)
- [Slide deck](./slides.pdf)
- [Discord chat](https://bit.ly/cassandra-workshop)
- [Questions and Answers](https://community.datastax.com/)

## Participation Badge / Homework

<img src="images/intro-to-nosql-badge.png?raw=true" width="200" align="right" />

To get the verified badge, you have to complete the following steps:

1. Complete the practice steps of this workshop as explained below. Steps 1-4 (Astra account + tabular/document/key-value databases) are mandatory, step 5 (graph database) is optional. Take a screenshot of completion of the last step for sections 2, 3 and 4 (either a CQL command output or a response in the Swagger UI). _NOTE: When taking screenshots ensure NOT to copy your Astra DB secrets!_
2. Submit the practice [here](https://dtsx.io/nosql-ws-hw), answering a few "theory" questions and also attaching the screenshots.
<!-- x. Complete [try-it-out scenario](https://www.datastax.com/try-it-out) and make a screenshot of the "scenario completed" screen -->

## Practice

1. [Login or Register to AstraDB and create database](#1-login-or-register-to-astradb-and-create-database)
2. [Tabular Databases](#2-tabular-databases)
3. [Document Databases](#3-document-databases)
4. [Key-Value Databases](#4-keyvalue-databases)
5. [Graph Databases](#5-graph-databases)

## 1. Login or Register to AstraDB and create database

**`ASTRADB`** is the simplest way to run Cassandra with zero operations at all - just push the button and get your cluster. No credit card required,
a monthly free credit to use, covering about 20M reads/writes and 80GB storage (sufficient to run small production workloads), all for FREE.

### ✅ 1a. Register a free account on Astra

Click the button below to login or register on DataStax Astra DB. You can use your `Github`, `Google` accounts or register with an `email`.

<a href="https://astra.dev/5-18"><img src="https://github.com/datastaxdevs/workshop-graphql-netflix/raw/master/img/create_astra_db.png?raw=true" /></a>

**Use the following values when creating the database** (this makes your life easier further on):

|Field| Value|
|---|---|
|**database name**| `workshops` |
|**keyspace**| `nosql1` |
|**Cloud Provider**| Stick to GCP and then pick an "unlocked" region to start immediately |

More info on account creation [here](https://awesome-astra.github.io/docs/pages/astra/create-account/).

You will see your new database as `pending` or `initializing` on the Dashboard.
The status will then change to `Active` when the database is ready: this will only take 2-3 minutes.
At that point you will also receive a confirmation email.

## 2. Tabular databases

In a tabular database we will store ... tables! The Astra DB Service is built on Apache Cassandra™, which is tabular. Let's start with this.

> **Tabular databases** organize data in rows and columns, but with a twist from the traditional RDBMS. Also known as wide-column stores or partitioned row stores, they provide the option to organize related rows in partitions that are stored together on the same replicas to allow fast queries. Unlike RDBMSs, the tabular format is not necessarily strict. For example, Apache Cassandra™ does not require all rows to contain values for all columns in the table. Like Key/Value and Document databases, Tabular databases use hashing to retrieve rows from the table. Examples include: Cassandra, HBase, and Google Bigtable.

### ✅ 2a. Describe your Keyspace

At database creation you provided a keyspace, a logical grouping for tables.
Let's visualize it.
In Astra DB go to CQL Console to enter the following commands

#### Select your db

![image](images/01.png?raw=true)

#### Go to the Cql Console
![image](images/02.png?raw=true)

#### Enter the describe command

... and press Enter:

```sql
DESCRIBE KEYSPACES;
```

![image](images/03.png?raw=true)

### ✅ 2b. Create table

#### Table creation

Execute the following Cassandra Query Language commands

```sql
USE nosql1;

CREATE TABLE IF NOT EXISTS accounts_by_user (
  user_id         UUID,
  account_id      UUID,
  account_type    TEXT,
  account_balance DECIMAL,
  user_name       TEXT      STATIC,
  user_email      TEXT      STATIC,
  PRIMARY KEY ( (user_id), account_id)
)   WITH CLUSTERING ORDER BY (account_id ASC);
```

#### Check

Check keyspace contents and structure:

```sql
DESCRIBE KEYSPACE nosql1;
```

_👁️ Expected output_

```
CREATE KEYSPACE nosql1 WITH replication = {'class': 'NetworkTopologyStrategy', 'eu-central-1': '3'}  AND durable_writes = true;

CREATE TABLE nosql1.accounts_by_user (
    user_id uuid,
    account_id uuid,
    account_balance decimal,
    account_type text,
    user_email text static,
    user_name text static,
    PRIMARY KEY (user_id, account_id)
) WITH CLUSTERING ORDER BY (account_id ASC)
    AND additional_write_policy = '99PERCENTILE'
    AND bloom_filter_fp_chance = 0.01
    AND caching = {'keys': 'ALL', 'rows_per_partition': 'NONE'}
    AND comment = ''
    AND compaction = {'class': 'org.apache.cassandra.db.compaction.UnifiedCompactionStrategy'}
    AND compression = {'chunk_length_in_kb': '64', 'class': 'org.apache.cassandra.io.compress.LZ4Compressor'}
    AND crc_check_chance = 1.0
    AND default_time_to_live = 0
    AND gc_grace_seconds = 864000
    AND max_index_interval = 2048
    AND memtable_flush_period_in_ms = 0
    AND min_index_interval = 128
    AND read_repair = 'BLOCKING'
    AND speculative_retry = '99PERCENTILE';
```

### ✅ 2c. Working with DATA

#### Insert some entries into the table

```sql
INSERT INTO accounts_by_user(user_id, account_id, account_balance, account_type, user_email, user_name)
VALUES(
    1cafb6a4-396c-4da1-8180-83531b6a41e3,
    811b56c3-cead-40d9-9a3d-e230dcd64f2f,
    1500,
    'Savings',
    'alice@example.org',
    'Alice'
);

INSERT INTO accounts_by_user(user_id, account_id, account_balance, account_type)
VALUES(
    1cafb6a4-396c-4da1-8180-83531b6a41e3,
    83428a85-5c8f-4398-8019-918d6e1d3a93,
    2500,
    'Checking'
);

INSERT INTO accounts_by_user(user_id, account_id, account_balance, account_type, user_email, user_name)
VALUES(
    0d2b2319-9c0b-4ecb-8953-98687f6a99ce,
    81def5e2-84f4-4885-a920-1c14d2be3c20,
    1000,
    'Checking',
    'bob@example.org',
    'Bob'
);
```

#### Read values

```sql
SELECT * FROM accounts_by_user;
```

> Such a full-table query is strongly discouraged in most distributed databases
> as it involves contacting many nodes to assemble the whole result dataset:
> here we are using it for learning purposes, not in production and on a table
> with very few rows!

_👁️ Expected output_

```
 user_id                              | account_id                           | user_email        | user_name | account_balance | account_type
--------------------------------------+--------------------------------------+-------------------+-----------+-----------------+--------------
 0d2b2319-9c0b-4ecb-8953-98687f6a99ce | 81def5e2-84f4-4885-a920-1c14d2be3c20 |   bob@example.org |       Bob |            1000 |     Checking
 1cafb6a4-396c-4da1-8180-83531b6a41e3 | 811b56c3-cead-40d9-9a3d-e230dcd64f2f | alice@example.org |     Alice |            1500 |      Savings
 1cafb6a4-396c-4da1-8180-83531b6a41e3 | 83428a85-5c8f-4398-8019-918d6e1d3a93 | alice@example.org |     Alice |            2500 |     Checking

(3 rows)
```

> Notice that all three rows are "filled with data", despite the second of the insertions above skipping the `user_email` and `user_name` columns:
> this is because these are **static columns** (i.e. associated to the whole partition) and their value had been written already in the first insertion.

#### Read by primary key

```sql
SELECT user_email, account_type, account_balance
  FROM accounts_by_user
  WHERE user_id=0d2b2319-9c0b-4ecb-8953-98687f6a99ce
    AND account_id=81def5e2-84f4-4885-a920-1c14d2be3c20;
```

_👁️ Expected output_

```
 user_email      | account_type | account_balance
-----------------+--------------+-----------------
 bob@example.org |     Checking |            1000

(1 rows)
```

### ✅ 2d. Working with PARTITIONS

But data can be grouped, we stored together what should be retrieved together.

#### Try a query not compatible with the data model

<details><summary>(Optional: click to expand)</summary>

```
SELECT account_id, account_type, account_balance
   FROM accounts_by_user
   WHERE account_id=81def5e2-84f4-4885-a920-1c14d2be3c20;
```

<!-- ```
InvalidRequest: Error from server: code=2200 [Invalid query] message="Cannot execute this query as it might involve data filtering and thus may have unpredictable performance. If you want to execute this query despite the performance unpredictability, use ALLOW FILTERING"
```
 -->

**`Yes, we know`**, and now let's see why.

```
TRACING ON;
SELECT account_id, account_type, account_balance
   FROM accounts_by_user
   WHERE account_id=81def5e2-84f4-4885-a920-1c14d2be3c20
   ALLOW FILTERING;
TRACING OFF;
```

> _Note_: `ALLOW FILTERING` is almost never to be used in production, we use it here to see what happens!

_👁️ Output_

```
 account_id                           | account_type | account_balance
--------------------------------------+--------------+-----------------
 81def5e2-84f4-4885-a920-1c14d2be3c20 |     Checking |            1000

(1 rows)
```

But also (_"Anatomy of a full-cluster scan"_):

```
Tracing session: e97b98b0-d146-11ec-a4e5-19251c2b96e1

 activity                                                                                                                   | timestamp                  | source      | source_elapsed | client
----------------------------------------------------------------------------------------------------------------------------+----------------------------+-------------+----------------+-----------------------------------------
                                                                                                         Execute CQL3 query | 2022-05-11 16:25:03.675000 | 10.0.63.218 |              0 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
 Parsing SELECT[....]_by_user\n   WHERE account_id=81def5e2-84f4-4885-a920-1c14d2be3c20\n   ALLOW FILTERING; [CoreThread-0] | 2022-05-11 16:25:03.676000 | 10.0.63.218 |            229 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                                                         Preparing statement [CoreThread-0] | 2022-05-11 16:25:03.676000 | 10.0.63.218 |            445 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                                                Computing ranges to query... [CoreThread-0] | 2022-05-11 16:25:03.681000 | 10.0.63.218 |           5970 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                         READS.RANGE_READ message received from /10.0.63.218 [CoreThread-9] | 2022-05-11 16:25:03.682000 | 10.0.31.189 |             -- | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                Submitting range requests on 25 ranges with a concurrency of 1 (0.0 rows per range expected) [CoreThread-0] | 2022-05-11 16:25:03.682000 | 10.0.63.218 |           6197 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                                       Submitted 1 concurrent range requests [CoreThread-0] | 2022-05-11 16:25:03.682000 | 10.0.63.218 |           6312 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                             Sending READS.RANGE_READ message to /10.0.32.75, size=227 bytes [CoreThread-9] | 2022-05-11 16:25:03.682000 | 10.0.63.218 |           6436 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                            Sending READS.RANGE_READ message to /10.0.31.189, size=227 bytes [CoreThread-8] | 2022-05-11 16:25:03.682000 | 10.0.63.218 |           6436 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                         READS.RANGE_READ message received from /10.0.63.218 [CoreThread-4] | 2022-05-11 16:25:03.683000 |  10.0.32.75 |             -- | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
             Executing seq scan across 0 sstables for (min(-9223372036854775808), min(-9223372036854775808)] [CoreThread-4] | 2022-05-11 16:25:03.683000 |  10.0.32.75 |            444 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
             Executing seq scan across 0 sstables for (min(-9223372036854775808), min(-9223372036854775808)] [CoreThread-9] | 2022-05-11 16:25:03.684000 | 10.0.31.189 |            356 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                                       Read 1 live rows and 0 tombstone ones [CoreThread-4] | 2022-05-11 16:25:03.684000 |  10.0.32.75 |            789 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                                       Read 1 live rows and 0 tombstone ones [CoreThread-9] | 2022-05-11 16:25:03.684000 | 10.0.31.189 |            731 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                          Enqueuing READS.RANGE_READ response to /10.0.32.75 [CoreThread-4] | 2022-05-11 16:25:03.684000 |  10.0.32.75 |            897 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                         Enqueuing READS.RANGE_READ response to /10.0.31.189 [CoreThread-9] | 2022-05-11 16:25:03.684000 | 10.0.31.189 |            731 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                            Sending READS.RANGE_READ message to /10.0.63.218, size=212 bytes [CoreThread-7] | 2022-05-11 16:25:03.684000 |  10.0.32.75 |            954 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                            Sending READS.RANGE_READ message to /10.0.63.218, size=212 bytes [CoreThread-1] | 2022-05-11 16:25:03.684000 | 10.0.31.189 |           1098 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                          READS.RANGE_READ message received from /10.0.32.75 [CoreThread-9] | 2022-05-11 16:25:03.685000 | 10.0.63.218 |           9626 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                         READS.RANGE_READ message received from /10.0.31.189 [CoreThread-1] | 2022-05-11 16:25:03.702000 | 10.0.63.218 |          27526 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                                        Processing response from /10.0.32.75 [CoreThread-0] | 2022-05-11 16:25:03.856000 | 10.0.63.218 |         181075 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                                       Processing response from /10.0.31.189 [CoreThread-0] | 2022-05-11 16:25:03.856000 | 10.0.63.218 |         181193 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
Didn't get enough response rows; actual rows per range: 0.04; remaining rows: 99, new concurrent requests: 1 [CoreThread-0] | 2022-05-11 16:25:03.856000 | 10.0.63.218 |         181384 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
                                                                                                           Request complete | 2022-05-11 16:25:03.856560 | 10.0.63.218 |         181560 | 2898:d2d9:30d9:4a4f:acec:3e3a:3a76:4a7b
```

</details>

#### Retrieve data from a whole partition

```sql
SELECT account_id, account_type, account_balance
  FROM accounts_by_user
  WHERE user_id=1cafb6a4-396c-4da1-8180-83531b6a41e3;
```

_👁️ Expected output_

```
 account_id                           | account_type | account_balance
--------------------------------------+--------------+-----------------
 811b56c3-cead-40d9-9a3d-e230dcd64f2f |      Savings |            1500
 83428a85-5c8f-4398-8019-918d6e1d3a93 |     Checking |            2500

(2 rows)
```

[🏠 Back to Table of Contents](#table-of-content)

## 3. Document Databases

Let's do some hands-on with document database queries.

> **Document databases** expand on the basic idea of key-value stores where “documents” are more complex, in that they contain data and each document is assigned a unique key, which is used to retrieve the document. These are designed for storing, retrieving, and managing document-oriented information, often stored as JSON. Since the Document database can inspect the document contents, the database can perform some additional retrieval processing. Unlike RDBMSs which require a static schema, Document databases have a flexible schema as defined by the document contents. Examples include: MongoDB and CouchDB. Note that some RDBMS and NoSQL databases outside of pure document stores are able to store and query JSON documents, including Cassandra.

### ✅ 3a. Cassandra native JSON support

It is not widely known, but Cassandra accepts JSON queries out of the box. You can find more information [here](https://docs.datastax.com/en/cql-oss/3.3/cql/cql_using/useInsertJSON.html).

<details><summary>Show native JSON support</summary>

#### JSON syntax for insertions

Insert data into Cassandra with JSON syntax:

```sql
INSERT INTO accounts_by_user JSON '{
  "user_id": "1cafb6a4-396c-4da1-8180-83531b6a41e3",
  "account_id": "811b56c3-cead-40d9-9a3d-e230dcd64f2f",
  "user_email": "alice@example.org",
  "user_name": "Alice",
  "account_type": "Savings",
  "account_balance": "8500"
}' ;
```

> Warning: missing fields in the provided JSON will entail explicit insertion of corresponding `null` values.

#### JSON output when querying

In the same way you can retrieve JSON out of Cassandra ([more info here](https://docs.datastax.com/en/cql-oss/3.3/cql/cql_using/useQueryJSON.html)).

```sql
SELECT JSON account_type, account_balance
  FROM accounts_by_user
  WHERE user_id=1cafb6a4-396c-4da1-8180-83531b6a41e3;
```

_👁️ Output_

```
 [json]
-------------------------------------------------------
  {"account_type": "Savings", "account_balance": 8500}
 {"account_type": "Checking", "account_balance": 2500}

(2 rows)
```

This JSON support is but a wrapper around access to the same fixed-schema
tables seen in the previous section ("Tabular").

</details>

### ✅ 3b. Create a token and open Swagger

We now turn to using Astra DB's Document API.

#### Token creation

To do so, first you need to create an Astra DB token, which will
be used for authentication to your database.

**Create a token with "Database Administrator" privileges following
the instructions here: [Create an Astra DB token](https://awesome-astra.github.io/docs/pages/astra/create-token/#c-procedure).**
(See also [the official docs on tokens](https://docs.datastax.com/en/astra/docs/manage-application-tokens.html).)

Keep the "token" ready to use (it is the long string starting with `AstraCS:.....`).

> **⚠️ Important**
> ```
> The instructor will show you on screen how to create a token 
> but will have to destroy to token immediately for security reasons.
> ```

#### Swagger UI

The Document API can be easily accessed through a Swagger UI:
go the "Connect" page, stay in the "Document API" subpage, and locate the URL under the "Launching Swagger UI" heading:

![image](images/connect.png?raw=true)

Locate the "documents" section in the Swagger UI. You are now ready to fire requests to the Document API.

![image](images/05.png?raw=true)

### ✅ 3c. Create a new empty collection

![Swagger 3c](images/swagger/swagger_3c.png)

- Access ***Create a new empty collection in a namespace***
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `body` use 

```json
{ "name": "users" }
```
- Click the `Execute` button

You will get an `HTTP 201 - Created` return code.

> _Note:_ the response you just got from actually calling the API endpoint
> is given under the "Server response" heading. Do not confuse it with
> the "Responses" found immediately after, which are simply a documentation
> of all possible response codes (and the return object they quote are static
> example JSONs).

<details><summary>Click to show a screenshot</summary>
  
![image](images/swagger_responses_annotated.png?raw=true)

</details>

### ✅ 3d. Create new documents

#### Add a first document

![Swagger 3d](images/swagger/swagger_3d.png)

- Access ***Create a new document***
- Click `Try it out` button
- Fill with Header `X-Cassandra-Token` with `AstraCS:...[your_token]...`
- For `namespace-id` use `nosql1`
- For `collection-id` use `users`
- For `body` use 

```json
{
    "accounts": [
        {
            "balance": "1000",
            "id": "81def5e2-84f4-4885-a920-1c14d2be3c20",
            "type": "Checking"
        }
    ],
    "email": "bob@example.org",
    "id": "0d2b2319-9c0b-4ecb-8953-98687f6a99ce",
    "name": "Bob"
}
```
- Click the `Execute` button

_👁️ Expected output (your `documentId` will be different)_

```json
{
  "documentId": "137d8609-87f6-4cb7-9506-e52f338e79e9"
}
```

#### Add another document

Repeat with the following body, which has _a different structure_:

```json
{
    "accounts": [
        {
            "balance": "2500",
            "id": "83428a85-5c8f-4398-8019-918d6e1d3a93",
            "type": "Checking"
        },
        {
            "balance": "1500",
            "id": "811b56c3-cead-40d9-9a3d-e230dcd64f2f",
            "type": "Savings"
        }
    ],
    "email": "alice@example.org",
    "id": "1cafb6a4-396c-4da1-8180-83531b6a41e3",
    "name": "Alice"
}
```

As before, the document will automatically be given an internal unique `documentId`.


### ✅ 3e. Retrieve a document by its ID

![Swagger 3e](images/swagger/swagger_3eB.png)

- Access ***Get a document***
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `collection-id` use `users`
- For `document-id` use Bob's `documentId` (e.g. `137d8609-87f6-4cb7-9506-e52f338e79e9` in the above sample output)
- Click the `Execute` button

_👁️ Expected output_

```json
{
  "documentId": "137d8609-87f6-4cb7-9506-e52f338e79e9",
  "data": {
    "accounts": [
      {
        "balance": "1000",
        "id": "81def5e2-84f4-4885-a920-1c14d2be3c20",
        "type": "Checking"
      }
    ],
    "email": "bob@example.org",
    "id": "0d2b2319-9c0b-4ecb-8953-98687f6a99ce",
    "name": "Bob"
  }
}
```


### ✅ 3f. Find all documents in a collection

![Swagger 3f](images/swagger/swagger_3fB.png)

- Access ***Search documents in a collection***
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `collection-id` use `users`

Leave other fields blank (in particular, every query is paged in Cassandra).

- Click the `Execute` button

_👁️ Expected output (take note of the `documentId`s of your output for later)_

```json
{
  "data": {
    "6d0aafd9-3c2c-461f-92c6-08322eaef5da": {
      "accounts": [
        {
          "balance": "2500",
          "id": "83428a85-5c8f-4398-8019-918d6e1d3a93",
          "type": "Checking"
        },
        {
          "balance": "1500",
          "id": "811b56c3-cead-40d9-9a3d-e230dcd64f2f",
          "type": "Savings"
        }
      ],
      "email": "alice@example.org",
      "id": "1cafb6a4-396c-4da1-8180-83531b6a41e3",
      "name": "Alice"
    },
    "137d8609-87f6-4cb7-9506-e52f338e79e9": {
      "accounts": [
        {
          "balance": "1000",
          "id": "81def5e2-84f4-4885-a920-1c14d2be3c20",
          "type": "Checking"
        }
      ],
      "email": "bob@example.org",
      "id": "0d2b2319-9c0b-4ecb-8953-98687f6a99ce",
      "name": "Bob"
    }
  }
}
```


### ✅ 3g. Search document with a "where" clause

The endpoint you just used can support [`where` clauses](https://docs.datastax.com/en/astra/docs/read-documents.html#_retrieving_a_document_using_a_where_clause) as well,
expressed as JSON. You don't need to navigate away from it do try the
following:

![Swagger 3g](images/swagger/swagger_3g.png)

- Access ***Search documents in a collection*** (you should be there already)
- Click `Try it out` button
- Fill Header `X-Cassandra-Token` with `<your_token>`
- For `namespace-id` use `nosql1`
- For `collection-id` use `users`
- For `where` use `{"name": {"$eq": "Alice"}}`
- Click the `Execute` button

*👁️ Expected output*

```json
{
  "data": {
    "6d0aafd9-3c2c-461f-92c6-08322eaef5da": {
      "accounts": [
        {
          "balance": "2500",
          "id": "83428a85-5c8f-4398-8019-918d6e1d3a93",
          "type": "Checking"
        },
        {
          "balance": "1500",
          "id": "811b56c3-cead-40d9-9a3d-e230dcd64f2f",
          "type": "Savings"
        }
      ],
      "email": "alice@example.org",
      "id": "1cafb6a4-396c-4da1-8180-83531b6a41e3",
      "name": "Alice"
    }
  }
}
```

[🏠 Back to Table of Contents](#table-of-content)

## 4. Key/Value Databases

> **Key/Value databases** are some of the simplest and yet powerful as all of the data within consists of an indexed key and a value. Key-value databases use a hashing mechanism, so that that given a key, the database can quickly retrieve the associated value. Hashing mechanisms provide constant time access, which means they maintain high performance even at large scale. The keys can be any type of object, but are typically a string. The values are generally opaque blobs (i.e. a sequence of bytes that the database does not interpret). Examples include: Redis, Amazon DynamoDB, Riak, and Oracle NoSQL database. Some tabular NoSQL databases, like Cassandra, can also service key/value needs.

### ✅ 4a. Create a table for Key/Value

Go to the CQL Console again and issue the following commands
to create a new, simple table with just two columns:

```sql
USE nosql1;

CREATE TABLE users_kv (
  key   TEXT PRIMARY KEY,
  value TEXT
);
```

### ✅ 4b. Populate the table

Insert into the table all the following entries.
Note that all inserted values, regardless of their "true" data type,
have been coerced into strings according to the table schema.
Also note how the keys are structured and how some entries reference other,
effectively creating a set of interconnected pieces of information on the users:

```sql
INSERT INTO users_kv (key, value) VALUES ('user:1cafb6a4-396c-4da1-8180-83531b6a41e3:name',       'Alice');
INSERT INTO users_kv (key, value) VALUES ('user:1cafb6a4-396c-4da1-8180-83531b6a41e3:email',      'alice@example.org');
INSERT INTO users_kv (key, value) VALUES ('user:1cafb6a4-396c-4da1-8180-83531b6a41e3:accounts',   '{83428a85-5c8f-4398-8019-918d6e1d3a93, 811b56c3-cead-40d9-9a3d-e230dcd64f2f}');

INSERT INTO users_kv (key, value) VALUES ('user:0d2b2319-9c0b-4ecb-8953-98687f6a99ce:name',       'Bob');
INSERT INTO users_kv (key, value) VALUES ('user:0d2b2319-9c0b-4ecb-8953-98687f6a99ce:email',      'bob@example.org');
INSERT INTO users_kv (key, value) VALUES ('user:0d2b2319-9c0b-4ecb-8953-98687f6a99ce:accounts',   '{81def5e2-84f4-4885-a920-1c14d2be3c20}');

INSERT INTO users_kv (key, value) VALUES ('account:83428a85-5c8f-4398-8019-918d6e1d3a93:type',    'Checking');
INSERT INTO users_kv (key, value) VALUES ('account:83428a85-5c8f-4398-8019-918d6e1d3a93:balance', '2500');

INSERT INTO users_kv (key, value) VALUES ('account:811b56c3-cead-40d9-9a3d-e230dcd64f2f:type',    'Savings');
INSERT INTO users_kv (key, value) VALUES ('account:811b56c3-cead-40d9-9a3d-e230dcd64f2f:balance', '1500');

INSERT INTO users_kv (key, value) VALUES ('account:81def5e2-84f4-4885-a920-1c14d2be3c20:type',    'Checking');
INSERT INTO users_kv (key, value) VALUES ('account:81def5e2-84f4-4885-a920-1c14d2be3c20:balance', '1000');
```

### ✅ 4c. Update a value

You can imagine an application "navigating the keys" (e.g, from an user to an account) for instance
when it must update a balance. The actual update would look like:

```sql
INSERT INTO users_kv (key, value) VALUES ('account:81def5e2-84f4-4885-a920-1c14d2be3c20:balance', '9000');
```

Let's check:

```sql
SELECT * FROM users_kv WHERE key = 'account:81def5e2-84f4-4885-a920-1c14d2be3c20:balance';
```

*👁️ Expected output*

```
 key                                                  | value
------------------------------------------------------+-------
 account:81def5e2-84f4-4885-a920-1c14d2be3c20:balance |  9000

(1 rows)
```

#### Alternative update syntax

The same result is obtained with

```sql
UPDATE users_kv SET value = '-500' WHERE key = 'account:81def5e2-84f4-4885-a920-1c14d2be3c20:balance';
```

indeed, in most key-value data stores, inserting and updating are one and the same operation
since the main goal is usually the highest performance (hence, row-existence checks are skipped altogether).

Thus, writing entries with the key of a pre-existing entry will simply overwrite the less recent values,
enabling a very efficient and simple deduplication strategy.

Check once more what's in the table:

```sql
SELECT * FROM users_kv ;
```

*👁️ Expected output*


```
 key                                                  | value
------------------------------------------------------+------------------------------------------------------------------------------
 account:81def5e2-84f4-4885-a920-1c14d2be3c20:balance |                                                                         -500
   user:0d2b2319-9c0b-4ecb-8953-98687f6a99ce:accounts |                                       {81def5e2-84f4-4885-a920-1c14d2be3c20}
 account:811b56c3-cead-40d9-9a3d-e230dcd64f2f:balance |                                                                         1500
   user:1cafb6a4-396c-4da1-8180-83531b6a41e3:accounts | {83428a85-5c8f-4398-8019-918d6e1d3a93, 811b56c3-cead-40d9-9a3d-e230dcd64f2f}
      user:1cafb6a4-396c-4da1-8180-83531b6a41e3:email |                                                            alice@example.org
       user:1cafb6a4-396c-4da1-8180-83531b6a41e3:name |                                                                        Alice
       user:0d2b2319-9c0b-4ecb-8953-98687f6a99ce:name |                                                                          Bob
      user:0d2b2319-9c0b-4ecb-8953-98687f6a99ce:email |                                                              bob@example.org
    account:83428a85-5c8f-4398-8019-918d6e1d3a93:type |                                                                     Checking
    account:811b56c3-cead-40d9-9a3d-e230dcd64f2f:type |                                                                      Savings
    account:81def5e2-84f4-4885-a920-1c14d2be3c20:type |                                                                     Checking
 account:83428a85-5c8f-4398-8019-918d6e1d3a93:balance |                                                                         2500

(12 rows)
```

[🏠 Back to Table of Contents](#table-of-content)

## 5. Graph Databases

> **Graph databases** store their data using a graph metaphor to exploit the relationships between data. Nodes in the graph represent data items, and edges represent the relationships between the data items. Graph databases are designed for highly complex and connected data, which outpaces the relationship and JOIN capabilities of an RDBMS. Graph databases are often exceptionally good at finding commonalities and anomalies among large data sets. Examples of Graph databases include DataStax Graph, Neo4J, JanusGraph, and Amazon Neptune.

Astra DB does not contain yet a way to implement Graph Databases use cases. But at Datastax we do have a product called [DataStax Graph](https://www.datastax.com/products/datastax-graph) that you can use for free when not in production.

For graph databases, the presenter will show a demo based on the example in the slides.

The hands-on practice for you is different. But since it cannot be done in the browser using
Astra DB like the rest, it is kept separate and not included in today's curriculum.

🔥 Yet, you are strongly encouraged to try it at your own pace, on your own computer,
by following the instructions given here: [Graph Databases Practice](graph_databases.md). 🔥

> Try it out, it's super cool!

## THE END

Congratulations! You made it to the END.

See you next time!

[🏠 Back to Table of Contents](#table-of-content)
