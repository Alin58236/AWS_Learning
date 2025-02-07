
### Bootstrapping EC2 Using User Data

- It allows EC2 Build Automation
- User Data is accessed using hte meta-data IP ( http://169.254.169.254/latest/user-data )
- Anything in user data is executed by the instanceOS
- ONLY ON LAUNCH
- EC2 Doesn't interpret the data, it just passes the data

![[Screenshot 2024-10-06 at 16.18.37 1.png]]

User Data key points are OPAQUE - just a block of data. It's not secure - don't use it for credentials.
It's limited to 16 kb of user-data. It can be changed when the instance stops, but only executes ***at Launch***.

- There is a metric called Boot-Time-To-Service-Time that indicates how long does the instance take to be ready as a service (receiving requests from customers)

--DEMO--

!! Better use cloudFormation !!


### EC2 Instance Roles

- Until now we've used IAM Roles to grant services the access that they need in order to interact with other AWS services or features. 

- For EC2 instances things are pretty similar, but here we will be using **Instance Profiles** to assign roles to instances
	- The *Instance Profile* is a wrapper over an **IAM Role** containing access policies, and it is the actual component that is associated to an instance so that it can assume a role. 
	- When we were creating an instance and we used to assign it an IAM Role, what was happening under the hood was: AWS was creating an instance profile with the same name as the IAM Role, and it was assigning this profile to the EC2 Instance, **BUT when we're using CloudFormation to create and manage EC2 instances, these 2 must be created manually and separately**
- EC2 Instance roles always use instance meta-data that are automatically rotated (always valid)
	- ( iam/security-credentials/credentials/**role-name** )
- It is a better alternative to adding security/access keys into an instance

![[Screenshot 2024-10-06 at 18.40.22.png]]



### SSM Parameter Store

The SSM Parameter store is a service which is part of Systems Manager which allows the storage and retrieval of parameters - string, stringlist or secure string.

The service supports encryption which integrates with KMS, versioning and can be secured using IAM.

The service integrates natively with many AWS services - and can be accessed using the CLI/APIs from anywhere with access to the AWS Public Space Endpoints.

### Logging on EC2

- is done through CloudWatch
- we need to install CW Agent on the EC2 instance and provide an agent config file
- The CW Agent also needs rights to interact with AWS ( **we already know that it is bad practice to leave long term credentials in the instance -- hard to manage at scale --  so it is better suited to create an IAM Role for the CW Agent** )
- we also need to have collectD installed on the ec2 instance. If we use amazon linux it will not be installed by default. To install it we need to first create :

`$sudo mkdir -p /usr/share/collectd/`
`$sudo touch /usr/share/collectd/types.db`


### EC2 Placement Groups

When you make an ec2 instance, AWS places it where it thinks it is best suited.

There are **3 types of Placement Groups**:

1. Cluster - close instances
2. Spread - Separated, on different hardware
3. Partition - groups of instances spread apart

#### 1. Cluster

All instances in the same AZ as the Placement Group (which is located in the same AZ EC2 Instance is first deployed) 

- Same Rach/Hardware/Host 
- 10Gbps p/ stream 
- lowest latency
- ONE AZ ONLY

#### 2. Spread

- Located on separate AZs, some in distinct Racks
- Limited to 7 instances per AZ
- Maximum availability
- provides Infrastructure isolation


#### 3. Partitions

- Divided into partitions
- MAX 7 per AZ
- Each partition has its own rack, not instances!!





