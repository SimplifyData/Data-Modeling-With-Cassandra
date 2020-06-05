# Data-Modeling-With-Cassandra
Create a NoSQL ETL Pipeline
The goal of this project is to define a data model that will help our data scientists answer questions about user activity. In this case, we are working with a NoSQL database, [Apache Cassandra](http://cassandra.apache.org/), which means we have to be especially intentional in our data modeling. We first need to understand what queries our data scientists would like to run, and then we can design tables to fit those queries. We will be aiming for "1 query, 1 table".

## File Overview
- `event_data/` - contains our data for user sessions in the streaming app in csv format. files are partitioned by day. (e.g. `2018-11-02-events.csv`)
- `images` - contains an image of what our data looks like after an ETL process
- `data_modeling_with_cassandra.ipynb` - Has two parts:
  - ETL pipeline for processing the event files and compiling them into a single csv with the desired columns
  - Data modeling examples that show three queries and the creation of three tables to serve those queries


## The Data Model: 1 Query, 1 Table

**Subset of our data after the ETL pipeline:**


### Queries to model for

**Song plays by session**

Query:
> Give me the artist, song title and song's length in the music app history that was heard during sessionId = 338, and itemInSession = 4

Required fields: artist, song, length, sessionid, itemInSession

Partition Key: should be `sessionId`, because this is the primary way our data is being filtered in the query. We are also filtering on `itemInSession`, but that field is less specific and would result in a less logical distribution across nodes.

Primary Key: `sessionId` is not sufficient for a PK, because it is not unique. However, adding `itemInSession` as a clustering column with give us uniqueness.

**Song plays by User's session**

Query:
> Give me only the following: name of artist, song (sorted by itemInSession) and user (first and last name) for userid = 10, sessionid = 182

Required fields: artist, song, itemInSession, firstName, lastName, userId, sessionId

Partition Key: should be `userId` in this case, because this is the primary way our data is being filtered in the query. We are also filtering on `sessionId`, but again that field is less specific and would result in a less logical distribution across nodes.

Primary Key: In this table `userId` would not be unique, and adding `sessionId` would still not guarantee uniqueness. However, the query also asks for sorting by `itemInSession`, which means we need it as a clustering column anyway. So a PK that is both unique and sorts the way we would like is `(userId, sessionId, itemInSession)`.

**Users by Song listens**

Query:
> Give me every user name (first and last) in my music app history who listened to the song 'All Hands Against His Own'

Required fields: firstName, lastName, song, userId

Partition Key: we're filtering by `song` in this case, so we'll use that as our partition key

Primary Key: It's not necessarily required given our query, but I've chosen to include the `userId` field so that we can add it to our PK. I've done this for two reasons, the first being that we need a unique PK and `(song)` or `(song, firstName)` don't suffice. Another reason would be to give us some sorting by `userId`, which feels like a logical way to order. If instead we wanted to sort alphabetically we could do `(song, firstName, userId)`.
