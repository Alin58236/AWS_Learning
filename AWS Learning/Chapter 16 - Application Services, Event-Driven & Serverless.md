
## Lambda

### AWS Lambda

- **Function-as-a-Service (FaaS) - *short running and focused***
- ***Lambda Function*** - a piece of code that Lambda runs
- Functions use a ***runtime*** (e.g. Python 3.8)
- Functions are loaded and run in a ***runtime environment***
- the environment has a ***direct memory ( indirect CPU )*** allocation
- ***Billed on the duration that a function runs for***
- Key part of ***serverless*** architecture

Most common runtimes:
- Python
- Ruby
- Java
- Go
- C#


#### Parameters


Docker is an **anti-pattern** for Lambda!!!

**Memory** : from 128 MB to 10240 MB
1 vCPU = 1769 MB

**Storage**: from 512 MB to 10240 MB 
available as ***/tmp***


***!!!! Lambda functions need to run in under 15 minutes.***

For longer periods of time we can use step-functions


#### Security & Networking


Lambda uses ***Execution Roles***, which are IAM roles assumed by the lambda function so it can interact with other AWS Services

Lambda Resource Policy Controls what services can trigger the Lambda Function

Lambda uses **CloudWatchLogs** for storing ***logs about executions*** (requires permissions via Execution Role)

Lambda uses **CloudWatch** to store metrics like ***success/failure,  retries, latency***

Can be integrated with **X-Ray for Distributed Tracing**

It has 2 networking modes:
##### 1. Public Networking (Default)

	- has access to public AWS Services and the public internet
	- Best performance because it doesn't require a customer specific VPC
	- it cannot access services inside a VPC since it is Private!!! (Unless the private services are configured to allow that access)

##### 2. Private Networking
	- Lambda funcitons located inside a VPC obey to all limitations/configs of that VPC
	- they can't access any internet facing public services
	- NAT Gateway and Internet Gateway are necessary for VPC Lambdas to access the public Internet
	- VPC endpoints can allow access to public AWS Services
	- For VPC to VPC communication for Lambdas, only one Gateway and ENI (Elastic Network Interface) is needed (because AWS manages the subnets and security groups for all Lambdas in the same region, in the same AWS Account)
	- These ENIs take 90s but only at the first Lambda run



#### Common Uses

- Serverless Apps (S3, API Gateway, Lambda)
- File Processing (S3, S3 Events, Lambda)
- DB Triggers ( Dynamo DB , Streams , Lambda)
- Serverless CRONJobs (EventBridge/ CWEvents , Lambda)
- Realtime Stream Data Processing (Kinesis + Lambda)



#### Types of Invocations!



1. Synchronous Invocation

- usually a CLI or API invokes a Lambda Function and it is passed some data
- in this time the API or CLI is waiting for a response (sync communication)
- Lambda responds regardless if it *succeeds* or *fails*
- ![[Screenshot 2025-07-25 at 17.15.17 1.png]]
- Generally used when a human is directly or indirectly invoking

2. Asynchronous Invocation

- Typically used when a AWS Service invokes the function
- Lets assume that something is uploaded to S3. This results in an event being created, and this event is invoking a lambda. S3 isn't waiting for any kind of response (async communication)
- Lambda is responsible for any retries or reprocessing
- Lambda NEEDS to be idempotent
- Events can be sent to DLQs in case of errors for diagnostics and additional checks
- NEW FEATURE! We can create destinations for Lambda Functions -> we can reproccess stuff


3. Event Source-Mapping

- typically used for streams or queues that don't support lambda triggering
- Usually used for batches that are sent to the lambda to process
- Any batches can be sent to an SQS or SNS to be further processed or analyzed


#### Versioning

Lambda functions can have versions

The versions consist of the code, and the configuration that is used to run the code

it's immutable - it never changes once published and has it's own ARN

$Latest points at the last version

Aliases can be created for versions (DEV, QA, PROD)

![[Screenshot 2025-07-28 at 13.42.52.png]]



### EventBridge & CloudWatch Events

- Near Real-time bridge of events
- Event Bridge is a superset over CloudWatch Events, but it can consume and handle events from 3rd parties (ActiveMQ, Kafka, RabbitMQ)

- If **X** happens, or at ***Y*** Time, than perform ***Z***

- **EventBridge** is basically CloudWatch Events v2
- a default Event Bus is created per account, In CloudWatch Events this is the only event bus

- Event Bridge can have additional busses
- Rules match incoming events or schedules
- Routes events to 1+ targets


### SNS (Simple Notification Service)

- Public AWS Service
- Coordinates the sending and delivery of messages (256KB)
- SNS Topic - the base entity of the SNS - configuration and permission
- PUB/SUB architecture


![[Screenshot 2025-07-29 at 11.02.09.png]]


![[Screenshot 2025-07-29 at 11.04.12.png]]



### SQS (Simple Queue Service)


- public 
- Fully Managed, Highly Available
- FIFO or Standard (messages could be received out of order)
- Messages up to 256 KB - link to large data
- After polling and receiving messages, the messages are not removed, they are only invisible (visibility timeout)
- If it is consumed and processed, the client will explicitly delete the message
- Then it can reappear in retry queues of be explicitly deleted
- ***Dead letter queues*** can be used for problematic messages

- Auto Scaling Groups can scale depending on the queue length!!!
- ![[Screenshot 2025-07-29 at 11.52.23.png]]



- Standard ( at-least-once ) , FIFO ( exactly-once )
- FIFO (performance) - 3000 messages per second with batches, 300 mps without
- Billed based on requests (one request can have one or more messages - 1-10 messages up to 64KB)
- Short (immediate) vs Long polling (waitTimeSeconds)
- Encryption at Rest (KMS) & in transit
- Identity policies or Queue Policies (only for access for external accounts)

#### Standard vs FIFO


- Fifo guarantees order
- ![[Screenshot 2025-07-29 at 12.16.51.png]]


#### SQS Extended Client Library

- used for messages larger that 256 KB (SQS Max)
- The large payloads are loaded in S3
- **SendMessage** uploads to **S3**, stores link in ***message***
- ***ReceiveMessage*** loads large payload from **S3**
- ***DeleteMessage*** also deletes the message from S3


#### SQS Delay Queues

- not supported on FIFO Queues

![[Screenshot 2025-07-29 at 12.35.51.png]]

#### SQS Dead Letter Queue


- uses a redrive policy (so that the maxReceiveCount doesn't go to infinite)
- when a mesage processing fails, it is moved to a DLQ
- Retention period of a DLQ is usually longer than the source queue's

### Step Functions


- long running serverless functions
- solves the problems or limitations of Lambda
	- Lambda is FaaS
	- You shouldn't put an entire app in a Lambda (processing under 15 minutes)
	- Lambdas can be chained together (but this gets messy at scale)
	- But each runtime environment is stateless

- State Machine max duration is 1 year
- ***Standard*** workflow and ***Express*** workflow
- Started via API Gateway, IOT rules, Event Bridge, Lambda
- **IAM ROLE** used for permissions
- ***Amazon States Language (ASL)*** - JSON Template
- ![[Screenshot 2025-07-29 at 13.28.18.png]]

![[Screenshot 2025-07-29 at 14.20.19.png]]



### API Gateway

- service that manages and interfaces APIs
- entry-point
- highly-available, scalable, handles Authentication, throttling, CORS, OpenAPI Specs
- can connect either AWS services or On-Prem
- HTTP/ REST/ WebSocket

#### Authentication

- APIs can be complete open access
- or they can use Authentication

- uses Cognito for tokens
- pass token with requests
- uses a Lambda Authorizer that returns an IAM Policy Principal

![[Screenshot 2025-07-29 at 18.31.25.png]]


#### Endpoint Types

- edge optimised (any incoming requests are routed to the nearest CloudFront POP)
- regional - clients in the same region
- private endpoint types - only accessible from a vpc via an interface endpoint


#### Stages

- APIs are deployed to ***STAGES*** each stage has a deployment
- Stages can be ***canary***

![[Screenshot 2025-07-29 at 18.34.02.png]]


#### Errors


Error codes generated by API Gateway fall into 2 categories:

- 4xx - Client Error, Invalid Requests on client side
- 5xx - Server Error - Valid Request, Server error

- 400 - Bad Request - generic
- 403 - access denied - authorizer denies.. WAF Filtered
- 429 - API Gateway can throttle - exceeded amount of requests
- 502 - bad gateway exception - bad output returned by lambda
- 503 - service unavailable - major service issues, backing endpoint offline?
- 504 - integration failure / Timeout - ***29s limit***


#### Caching 

- Caching is configured **per stage**

- reduced load and cost 
- calls are only made to the backend integrations if there is a cache miss
- cache TTL default is 300 seconds, configurable min 0 and max 3600s
- can be encrypted
- cache size 500MB - ***237GB***





## Kinesis


### Kinesis - Data Streams


- scalable streaming service
- Producers send data to the Kinesis Stream
- can scale from low to near infinite data streams
- public service
- Streams store a 24 h moving window of data ( up to 365 days for extra $)

- Streams scale by using shard architecture


![[Screenshot 2025-07-29 at 20.53.42.png]]


#### SQS vs Kinesis

![[Screenshot 2025-07-29 at 21.56.12.png]]



### Kinesis Data Firehose


- Makes kinesis consume data from/ publish to datalakes like S3
- helpful for analytics
- near real time ~ 60 seconds
- Automatic scaling, resilient, fully serverless
- Supports transformation of data on the fly (using lambda, but adds time overhead)

- can deliver data to http endpoints ***(3rd party), redshift, splunk, s3 bukcet, elasticsearch***
- 
![[Screenshot 2025-07-29 at 22.04.48.png]]


### Kinesis Data Analytics

- realtime processing of data
- uses SQL
- ingests from Kinesis Data Streams or Firehose
- Destinations
- Firehose (S3, Redshift, ElasticSearch, Splunk)
- AWS Lambda

![[Screenshot 2025-07-29 at 22.09.22.png]]


#### Kinesis Data Analytics - when and where

- Time series analytics - elections or esports
- Streaming data needing real time SQL processing
- dashboards in realtime (leaderboards for games)
- Real time metrics - security and response teams






## Cognito

- Core identity product in AWS
- Has Terrible Naming
- provides Authentication, Authorization, and user management for mobile/web apps

- ***USER POOL - Sign-in and get a JWT***
	- BUT most aws services cannot use JWT.. 
	- user directory management and profiles, signup signin (customisable web UI), MFA, and other security features
	- Also provide 3rd party auth with social credentials (facebook, google)
	- ***imagine a db of users (they just signin and get a JWT - THATS IT***)

- ***IDENTITY POOLS - Allow you to offer access to Temporary AWS Credentials***
	- unauthenticated identities - Guest Users
	- federated identities - SWAP , google, facebook, twitter, SAML 2.0, & user pool for short term AWS Credentials to access AWS Resources.
	- These pools work by assuming an IAM role...


#### User Pool Architecture 

![[Screenshot 2025-07-29 at 22.19.02.png]]

#### Identity Pool Architecture

Note !  - for 5 different identity providers, we need 5 different configurations for IAM Role distribution

![[Screenshot 2025-07-29 at 22.21.42.png]]


#### Architecture for Both


![[Screenshot 2025-07-29 at 22.22.50.png]]