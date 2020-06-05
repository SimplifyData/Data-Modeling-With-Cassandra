# Data-Modeling-With-Cassandra
Create a NoSQL ETL Pipeline
The goal of this project is to define a data model that will help our data scientists answer questions about user activity. In this case, we are working with a NoSQL database, [Apache Cassandra](http://cassandra.apache.org/), which means we have to be especially intentional in our data modeling. We first need to understand what queries our data scientists would like to run, and then we can design tables to fit those queries. We will be aiming for "1 query, 1 table".

## File Overview
- `event_data/` - contains our data for user sessions in the streaming app in csv format. files are partitioned by day. (e.g. `2018-11-02-events.csv`)
- `images` - contains an image of what our data looks like after an ETL process
- `data_modeling_with_cassandra.ipynb` - Has two parts:
  - ETL pipeline for processing the event files and compiling them into a single csv with the desired columns
  - Data modeling examples that show three queries and the creation of three tables to serve those queries


