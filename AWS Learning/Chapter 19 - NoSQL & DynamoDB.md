### DynamoDB

- NOSQL **Public** Database-as-a-Service
- Key/Value & Document
- No self-managed servers, you actually get the DB

- Manual or Automatic provisioned performance IN/OUT or On-Demand
- Highly resilient, across AZs or global
- Really fast -> single digit milliseconds (has SSDs)

The unit in DymanoDB is a **TABLE**

Tables are groupings of **ITEMS** which have the same PRIMARY KEY

- The primary key can be ***simple (partition)***, or ***composite (partition + sort Key)***

The combination of PK (partition Key) and SK (sort Key) must be unique, the rest of the attributes are completely optional (like mongo)

- Item Max size mut be 400KB

- The capacity (speed) is set at the Table Level 
	- 1 RCU (Read Capacity Unit) = 4KBps
	- 1 WCU (Write Capacity Unit) = 1KBps


It has on-demand backups.

Can also restore in the same, or cross-region

Also has Point-In-Time-Recovery (PITR) - disabled by default - has a 35 day window



### Operations, Consistency and Performance

- We can pick between 2 capacity modes:
	- ***On Demand*** - unknown, unpredictable, low admin
		- price per million read-write units

	- ***Provisioned*** - RCU and WCU set on a table basis
		- every operation consumes at least 1 RCU or WCU

When we perform a query on a DynamoDB Table we need to start with the Partition Key - and optionally an SK . Each query operation can return 0, 1 or more values. 

If a Query returns less that 4KB, it will still be charged as one RCU.

![[Screenshot 2025-08-01 at 00.08.29.png]]

If you want to perform more flexible operation, we need to use ***SCAN***

The way scan works is to move through the table item by item. -> It consumes EVERYTHING in the table even if it is not returned


#### Consistency Model

It uses 2 different consistency modes:
1. eventually consistent - easier to implement and scales better
2. strong consistency - more costly and scales harder

![[Screenshot 2025-08-01 at 00.22.35.png]]

##### How to determine appropriate values for capacity of a table

1. 
If you need to store 10 ITEMS per second .. 2.5 KB average size per ITEM

Calculate WCU per item .. ROUND UP (ITEM SIZE / 1KB) ->

so for 2.5K it is 3 WCU

multiply by average number per second (10)  -> 

in this example is 30


2. 
For reads lets say we need to retrieve 10 items per second 2.5KB average in size

Calculate RCU per item ... ROUND UP ( ITEM SIZE / 4KB ) (1)

Multiply by average read ops per second (10)

= Strongly Consistent RCU Required (10)

= Eventually Consistent RCU Required (5)


### Indexes (LSI & GSI)

- there are 2 types of indexes for DynamoDB:
	1.  LSI (Local Secondary Index)
	2. GSI (Global Secondary Index)

The indexes in DynamoDB don't represent pointers like in another DBs. They represent alternative views to a base table. They allow us to use different attributes to query for.


#### Local Secondary Indexes

- ***Must*** be created ***with a base table***
- 5 LSI per base table
- Alternative Sort Key (SK), but same PK
- Shares the ***RCU and WCU*** with the ***Table***
- Attributes - **ALL, KEYS_ONLY & INCLUDE**


ex. Let's assume that we have a table that keeps record of multiple days forecasts for multiple weather stations.

For each weather station there is one record created daily. If we want to stick to using the QUERY operation, then we are limited to querying for a single weather station (PK), and for that weather station we can filter for a single day or a range of days (SK).

But what if we want to query after "SUNNY DAY", which is another attribute.
While we are creating this table we can also create a LSI for that SUNNY DAY Attribute.

So we will use the same PK (weather station), but different SK (SUNNY DAY).

Indexes are sparse, only items which have a value in the index alternative sort key are added to the index.

![[Screenshot 2025-08-03 at 22.15.35.png]]



#### Global Secondary Indexes (GSI)

- can be created at any time
- Default limit of 20 per base table
- Alternative PK and SK
- Have their own RCU and WCU
- Attributes - ALL, KEYS_ONLY, INCLUDE


![[Screenshot 2025-08-03 at 22.18.35.png]]

GSI are always eventually consistent.


#### Considerations

- Queries on attributes which are not projected are REALLY expensive
- Use GSIs as default, LSI only when Strong Consistency is required
- use indexes for alternative access patterns

### Streams and Triggers

#### Streams

- are enabled on a per-table basis.
- 24h rolling window
- time ordered list of ITEM CHANGES in a table
- actually uses KINESIS
- Records INSERTS, UPDATES, and DELETES

- Different view types influence what is in the stream
 1. KEYS_ONLY - shows only the keys of the record that was changed
 2. NEW_IMAGE - shows the new state after the change
 3. OLD_IMAGE - shows the previous state that was changed
 4. NEW_AND_OLD_IMAGES - shows both new and old state for comparison

#### Triggers

- these allow for events to start when a change is being made
- ITEM changes generates an event
- that event contains the data which changed
- an action is taken using that data
- AWS = Streams + Lambda
- Reporting and Analytics
- Aggregation, Messaging or Notification

![[Screenshot 2025-08-03 at 22.39.16.png]]



### DynamoDB Accelerator (DAX)

- an elastic cache for DynamoDB

- ![[Screenshot 2025-08-03 at 22.51.59.png]]


![[Screenshot 2025-08-03 at 23.44.49.png]]


![[Screenshot 2025-08-03 at 23.46.52.png]]


### Global Tables

- global tables provides multi-master cross-region replication
- tables are created in multiple regions and added to the same global table (becoming replica tables)
- ***Last writer wins*** - used for conflict resolution
- **Reads** and **Writes** can occur to ***any region***
- generally sub-second replication between regions
- Strongly consistent reads **ONLY** in the same region as writes


### Time-To-Live (TTL)

- per attribute basis
- ![[Screenshot 2025-08-03 at 23.57.31.png]]



### Elasticache

- in memory DB -> high performance
- Managed **REDIS** and **MEMCAHCED** as a service
- Can be used to cache data - for **READ HEAVY** workloads with low latency requirements
- reduces database workloads -> cost effective
- can be used to store **Session Data** (Stateless Servers)
- ****Requires application code changes!!!!!****

#### Memcached

- simple data structures only - strings
- no replication
- Multiple Nodes (sharding)
- no backups
- multi-threaded by design

#### Redis

- strings, lists, sets, sortedSets
- multi AZ
- Replication (Scale Reads)
- Backup and Restore
- Transactions


### Athena 101

- it's a ***serverless interactive querying service***
- ad-hoc queries on data -> pay only data consumed
- Schema-On-Read - table like translation
- Original data never changed - remains on S3
- Schema translates data -> relational-like when read

- we can define schemas like tables for reading multiple formats of data
- these "tables" are defined in advance in a data catalog, and data is projected through when read


![[Screenshot 2025-08-04 at 00.26.49.png]]

**Great for :**
- data that doesn't need loading or transformation
- occasional / ad-hoc queries on S3
- serverless querying - cost conscious
- Querying AWS Logs - VPC flow logs, cloudTrail, ELB Logs, cost reports
- AWS Glue Data Catalog & Web Server Logs
- w/ Athena Federated Query -> other data sources
- pre-built connectors (JDBC, AWS Stuff)


