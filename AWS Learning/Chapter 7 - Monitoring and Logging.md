## CloudWatch architecture

- ingestion, storage and management of METRICS
- public service - public space endpoints
- Can be integrated either by default by AWS services, by using a CW Agent, or with On-prem integration or Application Integration ( for custom metrics )
- We can view data by using the CLI or UI
- Has configurable Alarms (they have 2 states : OK & ALARM, which can trigger events using another services and trigger actual alerts to notify the users)

## CloudWatch Data

The Data displayed is made out of several components:
1. Namespace (acts as a container for metrics) ex. AWS/EC2 or AWS/Lambda
2. Datapoint (the actual data that comprises the metrics) ex. Timestamp, Value, Unit of measure
3. Metric (time ordered) = a set of datapoints
4. CPUUtilization, NetworkIn, DiskWriteBytes
5. Every metric has a metric name (CPUUtilization) and a namespace (AWS/EC2)
6. Resolution (metrics over a specific period of time) ex. 60s timeframes for metrics

There is a downfall, if we want to separate the metrics for our different instances as CPUUtilization in AWS/EC2 displays the CPUUtilization for all EC2 instances running on that account.
For the separation we use "Dimensions".

Dimensions are Key-Value pairs ex. Name = [instance name]

*ex. CPUUtilization Name = InstanceId, Value = Instance-11111*


**! As data ages, it is aggregated and stored for longer periods of time at less resolution (ex. 1h resolution) !**

For Statistics we can aggregate metrics in custom manners.

![[Screenshot 2024-07-01 at 17.30.03.png]]



## Cloudwatch Logs Architecture

- Has 2 sides of the product : ingestion and subscription
- public Service - store, monitor, access logging data
- AWS, On-prem, IOT or any app
- Used by installing the CloudWatch Agent
- VPC Flow Logs
- **CloudTrail** used for AWS Account monitoring of account events and API Calls
- Route53 Monitoring for DNS requests
- ECS, Lambda Logging
- 

Structure:
- **The log groups contain multiple log streams which contain the log events

- The things like retention, permission and encryption are set at the log group level

- the logs are stored indefinitely but this can be changed


- We can export These logs from the streams to an S3 using Subscription (NOT REALTIME)

- Realtime can be achieved on a per log group basis using a Subscription Filter, Kinesis FireHose and a S3 (near realtime)

- for logging aggregation :
- a metric filter can be added to the log group

![[Screenshot 2024-07-01 at 17.37.40.png]]


## AWS X-RAY

- AWS service for distributed tracing
- Data components

![[Screenshot 2024-07-01 at 17.45.44.png]]

- very good for monitoring the waiting times and bottlenecks between services
- There are EC2 X-Ray agents, but there are services like Lambda that have an enable option
- ##### REQUIRES IAM PERMISSIONS


## VPC Flow Logs 

- useful networking feature for logging packet metadata (NOT THE CONTENTS) inside a VPC
- For contents inside packets we use packet sniffers
- Can be attached to a VPC, a subnet, or to an ENI (Elastic Network Interface)
- **Not Realtime**
- Destinations : S3 or CW Logs Console
- Compatible with Athena for querying
- ![[Screenshot 2024-07-01 at 18.01.43.png]]

Logs structure:
![[Screenshot 2024-07-01 at 18.02.17.png]]





# Next Chapter : [[Chapter 8 - ECS & Containers]]

#AWS_Learning 

