
### DBs on EC2

- Don't put DBs on EC2 !!!
	- Costly
	- Inefficient (EC2 may fail)
	- Non-Persistent env without Elastic Block Storages
	- Administrative Overhead
	- Backups / DR management
	- EC2 is single AZ!!!
	- No scaling... No Serverless
	- 

- might need it only if
	- you need ROOT / OS access... (super rare situation)
	- DB or DB access AWS don't provide
	- Business demands so... wtf


### Relational Database Service (RDS)

- it is not a DB, it is a DB Server!!!
- You can have multiple dbs on the server
- Can host multiple db engines ( MySQL, MariaDB, PostgreSQL, Oracle, Microsoft SQL Server)
- Amazon Aurora* Is a COMPLETELY DIFFERENT PRODUCT!

- Managed Service -> No OS Access or SSH!

#### Architecture 

- it is a private service ( unlike S3 or DynamoDB )
- it needs to run in a subnet of a VPC
- each db primary instance has an EBS Storage
- ![[Screenshot 2025-06-07 at 12.59.13.png]]

- RDS Supports Backups to S3 and Async Replication to other AZs or Regions, as well as SYNCHRONOUS REPLICATION TO STANDBYs!!!!!!!!

#### Costs
- billed like EC2, because it's based on it
- it is billed for resource allocation

1. Instance Size and Type
2. Multi AZs or NOT
3. Storage Type and amount
4. Data transferred
5. Backups and Snapshots (2tb free)
6. Licensing* (if applicable)

### Multi AZ Instance Deployments

- Data replicated to replica ( Synchronous, committed)
- Not free tier -> because of the cost of the replica
- One StandBy Replica **ONLY**
- which can't be used for reads or writes
- Same Region Only!!!
- Backups taken from standby to improve performance


RDS has the capability of having a **Writer Instance** that replicates the data to Reader replicas

The replication is committed when 1+ reader finishes writing


There are multplie endpoints available:
1. Cluster Endpoint (reader, writer, administration)
2. Reader Endpoint
3. Instance Endpoints (only used for testing, not good to use in-app because the cluster might be rebalancing)

Advantages of the MultiAZ Cluster Mode:

-  1 Writer and 2 Reader DB instances (different AZs)
-  Much faster hardware, Graviton + local NVME SSD Storage
- Fast writes to local storage -> flushed to EBS
- Readers can be used only for Reads, allowing some read scaling
- Replication via transaction logs
- Failover is faster ~35 s compared to 60-120s
- Writes are "committed" when one reader has confirmed

![[Screenshot 2025-06-19 at 16.32.35.png]]


### RDS Automatic Backups and Snapshots

We have either automatic backups or snapshots in RDS

Both are saved in S3 (AWS Managed S3 Buckets)

These backups are taken from the standby instances


#### Manual Snapshots

- The first Snapshot is FULL
- The next ones only save the data that was changed
- They are less effective if a lot of data is saved between snapshots (a lot of traffic)


#### Automatic Backups

- Automatic backups can be taken of an RDS instance with a 0 (Disabled) to 35 Day retention.

- Automatic backups also use S3 for storing transaction logs every 5 minutes - allowing for point in time recovery.


#### Cross-Region Backups 

- RDS can replicate backups to another region
- Charges apply for cross-region data copy
- Snapshots can be restored .. but create a new RDS instance.



### RDS Read Replicas

RDS Read Replicas can be added to an RDS Instance - 5 direct per primary instance.

They should be seen as separate from the DB, the applications must be specifically configured to use them.

They can be in the same region, or cross-region replicas.

They provide read performance scaling for the instance, but also offer low RTO recovery for any instance failure issues

N.B they don't help with data corruption as the corruption will be replicated to the RR.


**!! PRO TIP !!**

Synchronous means Multi-AZ
Asynchronous means Read Replicas


#### RPO/RTO Improvements

- Snapshots and Backups Improve RPO
- TRO's are a problem
- Read Replicas offer Near 0 RPO
- Read Replicas can be promoted quickly - low RTO
- Failure Only - watch for data corruption



### RDS Data Security

- SSL/TLS (in transit) is available for RDS, can be mandatory
- RDS Supports EBS Volume encryption - KMS
- Handled by Host/EBS
- AWS or customer Managed CMK generates data keys
- Data Keys used for encryption operations
- Storage, Logs, Snapshots & replicas are encrypted

***!!!! encryption can't be removed !!!!***


- MSSQL and RDS Oracle support TDE (Transparent Data Encryption)
- This kind of encryption is handled within the DB engine


#### Architecture

![[Screenshot 2025-06-19 at 18.19.51.png]]


### RDS IAM Authentication

- Normally access to DB is done using a local database of users
- We can configure the DB to allow IAM user authentication

We start with an RDS instance on which we create a Local DB account configured to use AWS Auth Token


We have IAM users and roles, in this case it is an instance Role, and we have a policy attached to the user.


This policy contains a mapping that maps the IAM Identity onto the local RDS user


This allows the entity to run a ***generate-db-auth-token*** operation (which works with IAM) which creates a token with 15 minute validity which can be used in place of a DB User Password.


THIS IS ONLY AUTHN! AUTHZ is managed by the DB engine!!!!!!!

![[Screenshot 2025-06-19 at 18.26.47.png]]



### Amazon Aurora Architecture




The architecture is ***VERY*** different from RDS

It uses a cluster with one primary instance and 0 or more replicas

Aurora doesn't use a local storage, it has a shared storage volume at a cluster level

- Faster provisioning
- improved performance
- higher availability

The shared storage is an ssd max to 128 TiB. All the replicas have access to all the storage nodes.
The Storage segments are managed by AWS -> when a part of that storage fails, Aurora immediately repairs that part of the storage -> NO POINT IN TIME RESTORES because it uses data from other segments with no corruption

- All Storage is SSD Based - high IOPS, low latency
- It is billed differently than normal RDS engines
- Up to 128 Tib Limit, you are billed per consumption
- Storage that is freed up can be reused
- Replicas can be added and removed without needing storage provisioning
- Backups work the same as RDS
- ***Restores*** create a new Cluster
- ***Backtracks*** restore to a previous point in time
- ***Fast Cloning*** makes a **Much** faster copy of the DB ( copy on write )
- 


#### Endpoints

1. Cluster Endpoint -> always points at the primary instance
2. Reader Endpoint -> points to primary if there are no replicas, points to ALL replicas if there are any ( load balancer )

#### Billing 

- No Free Tier
- For Compute billing is hourly charge, per second, 10 minute minimum
- Storage - GB/Month consumed, IO cost per request
- 100% DB Size backups are included


### Aurora Serverless

- Service which is to Aurora what Fargate is to ECS
- You don't need to statically provide a DB Instance of a certain size or manage them
- Scalable - ***ACU*** - Aurora Capacity Units
- Aurora Serverless has a ***MIN and MAX ACU***
- Cluster adjusts based on load
- Can go to 0 and be paused (less billing)
- Billing per second
- Same resilience as Aurora Provisioned ( 6 AZs )

The ACUs are allocated by AWS from a common pool based on load

![[Screenshot 2025-06-20 at 00.17.36.png]]


#### Use Cases

- Infrequently used application
- New applications
- Variable Workloads
- Unpredictable workloads
- DEV and QA DBs
- Multi-tenant application



### Secrets Manager

- different than SSM Parameter Store
- it does have functionality like the parameter store
- designed for secrets
- supports **automatic rotation** -> uses Lambda

![[Screenshot 2025-06-20 at 00.23.55.png]]

Secrets are encrypted using KMS!!!



Next Chapter: [[Chapter 13 - EFS ( Elastic File System)]]

#AWS_Learning 
