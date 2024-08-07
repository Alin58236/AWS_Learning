## Virtualization 101

- purpose of virtualization is to allow multiple OS to run in privileged mode (access to hardware)
1. the first type of virtualization was "emulated virtualization" and included a hypervisor that ran in privileged mode (host OS) and managed the virtual machines' resources
	- really slow due to binary translation done by the hypervisor
	
2. Paravirtualization was the second option, that looked kinda similar but it works on small subsets of OSs.
	- Only certain areas of the OS perform "hyper-calls" to the hypervisor - massively improves performance

3. The major improvement came when the hardware became virtualization aware


## Architecture and Resilience

- The **EC2 instance is a VM** (OS + resources)
- It runs on a EC2 Host (actual machine)
- ***Shared Hosts*** (shared across different AWS accounts), or ***Dedicated Hosts*** (pay for the entire host)
- **Hosts run inside a single AZ -> AZ fails -> Host Fails -> Instances Fail**

- Each **AZ has one or more Hosts** and ***each Host has an Instance Store ***(temporary store, if the EC2 Instance is moved, the data kept in the instance store is lost)
- Each Subnet where we keep an EC2 instance is allocated an elastic network card
- ![[Screenshot 2024-03-26 at 15.54.13.png]]
### **What is EC2 good for?**

- Traditional OS + App compute (services)
- *Long-Running Compute*
- Server style applications with **burst** or **steady-state** loads
- Monolithic apps
- Migrated application workloads or disaster recovery


## EC2 Instance types

- feature different ***Raw CPU***, **Memory**, *Local Storage Capacity and Type*
- Resource Ratios (some types prioritise CPU, others memory, etc.)
- Storage and Data network bandwidth
- System Architecture / Vendor (x86 vs x64, intel vs AMD ,etc)

#### Main Categories

1. **general purpose**
	- Default - diverse workloads, equal resource ratio

2. **compute optimised**
	- Media Processing, Scientific modelling, gaming, machine learning

3. **memory optimised**
	- Processing large in-memory datasets / caching, some database workloads

4. **accelerated computing**
	- Hardware GPU, field programmable gate arrays (FPGA - highly customisable hardware)

5. **storage optimised**
	- Sequential and random IO, scale-out transactional databases, data warehousing, **ElasticSearch**


#### Naming Scheme

![[Screenshot 2024-03-26 at 16.12.41.png]]

- The latest generation provides best price to performance

![[Screenshot 2024-03-26 at 16.19.40.png]]
For more details : https://instances.vantage.sh/

### NOTE! Connection through SSH vs EC2 Instance Connect
## Storage

#### There are different types of storage:
1. **Direct (local) attached storage** - Storage on the EC2 Host
2. **Network attached storage** - EBS - delivered over the network
3. **Ephemeral Storage** - temporary storage
4. **Persistent Storage** - IS PERMANENT - outlives the EC2 Instance lifetime

#### Categories of Storage:
1. **Block Storage** 
  - Volume presented to the OS as a collection of blocks 
  - No Structure 
  - **Mountable, Bootable** 
  - HDD or SSD, or logical volume	
  - most EC2 instances use it as a boot storage

2. File Storage - Presented as a file share -> has structure. **Mountable, NOT BOOTABLE**

3. Object Storage - Collection of objects - Flat Structure - **NOT MOUNTABLE, NOT BOOTABLE**

#### Storage Performance

*Depends on:*
- IO (block) size = 16kb -> 1MB
- IOPS = ops per second (100 maximum)
- Throughput 
###### *IO x IOPS = throughput*






## Elastic Block Store (EBS)

- **Block Storage:** EBS provides raw block-level storage that can be formatted with a filesystem (e.g., ext3, ext4, xfs).

- **Encryption:** EBS volumes can be encrypted using AWS Key Management Service (KMS).

- **File System:** Instances recognize the block device and can create a file system on this device.

- **Single Availability Zone (AZ):** EBS volumes are provisioned in a single AZ. This means they are tied to the specific AZ where they were created.

- **Attachment:** An EBS volume can be attached to a single EC2 instance at a time within the same AZ over a storage network.

- **Detach and Reattach:** EBS volumes can be detached from one instance and reattached to another instance within the same AZ. They are not tied to the lifecycle of an instance.

- **Persistence:** If an instance moves from one host to another (e.g., due to failure or scaling operations), the EBS volume remains attached to the instance, ensuring data persistence.

- **Backups:** EBS snapshots can be taken and stored in Amazon S3, allowing you to migrate data between different AZs or even different regions.

- **Types and Performance:** EBS offers different storage types (e.g., gp2, gp3, io1, io2, st1, sc1), each with distinct sizes and performance profiles to match various workloads.

- **Billing:** EBS is billed based on the provisioned storage size in GB/month and additional charges may apply for IOPS and data transfer depending on the volume type.

Architecture:![[Screenshot 2024-03-27 at 15.28.01.png]]

#### EBS Volume Types:

1. **General purpose SSD (GP2)**
	- between 1GB and 16TB
	- You must be aware of the baseline credit refill rate (100  IOPS)
	- ![[Screenshot 2024-03-27 at 15.35.43.png]]
	- Usages: boot volumes, virtual desktops, dev and test environment

2. **General Purpose SSD (GP3)**
	- No credit bucket architecture
	- Starts with a 3000 IOPS & 125 miB/s - Standard
	- Base price is 20% cheaper than GP2
	- Extra cost for 16k IOPS
	- 4x faster max throughput
	- Usages: boot volumes, virtual desktops, dev and test environment

3. **Provisioned IOPS SSD (IO1/2/Block Express)**
	- IOPS configurable independent of the size of the volume
	- *64000 IOPS per volume & 1000Mb throughput*
	- *256000 IOPS per volume (Block Express) and 4000MB/s throughput*
	
	- **Consistent Low Latency and Jitter**
	- ![[Screenshot 2024-03-27 at 15.44.51.png]]
	- Usages: High Performance, Low Latency, I/O intensive NoSQL

4. ***EBS - HDD-Based (st1)***
	- **St1 is designed for sequential data access**
	- 125GB - 1TB
	- Max 500 IOPS (1MB)
	- Max 500 MB/s
	- 40 MB / s / TB Base
	- 250 MB / s / TB Burst

5. ***EBS - HDD-Based (sc1)***
	- **Sc1 is designed for colder data requiring fewer scans per day**
	- Max 250 IOPS
	- Significantly Cheaper
	- 12 MB/s/TB Base
	- 80 MB/s/TB Burst

6. ***Instance Store Volumes***
	- Block Storage Devices
	- Like EBS but local and physically connected, instead of on the network
	- Instances on that host can access them
	- **Highest storage performance in AWS**
	- ***Included in instance price!!!***
	- ***Attached at launched***
	- ![[Screenshot 2024-03-27 at 18.27.26 1.png]]
	- Instance Store perform at much higher throughput
	- **Can only be attached at launch**


### EBS vs Instance Store

- *Persistence* -> ***EBS*** (instance store is lost when rebooting or migrating to other host)
- Resilience -> ***EBS***
- Isolation from instance lifecycle -> ***EBS***
- *Resilience* with an app that has built-in replication -> ***Instance store***
- High-Performance -> it depends but usually ***Instance Store***
- Cost -> ***Instance Store*** (it's included with the EC2)

- ![[Screenshot 2024-03-27 at 19.32.07.png]]

## Snapshots

- incremental volume copies to S3
- the first is a full copy of 'data' on the volume
- future snapshots are incremental
- volumes can be created or restored from snapshots
- can be used for migrations to other AZs/Regions
- ![[Screenshot 2024-03-27 at 19.43.21.png]]

- Snapshots restore **gradually** 

### !!! DEMO Lesson
- when creating an instance and an ebs volume, the volume is not attached to the instance, we must attach it manually.
- after attaching the ebs volume to the instance, we must create a file system on that volume in order to make it usable
- to access the filesystem we must mount it on the folder that we manually create
- after closing the instance, the filesystem is unmounted
<<<<<<< HEAD
- to mount it automatically at startup, the volume UUID must be set in the fstab config file
- SNAPSHOTS cannot only be used to transfer volumes from one AZ to another, but we can also copy them to different regions


## EBS Encryption

- Heavily dependent on KMS
- Needs a KMS key
- When an Encrypted volume is created, the KMS Service creates a DEK (*Data Encryption Key*) for that volume only
- It is created using the "**GenerateDataKey WithoutPlainText**" API call
- the encrypted key is stored on the EBS volume
- the plain text key is only loaded in the memory of the EC2 Host to be used for data encryption by the EC2 instance
- When the data is sent to be stored in the EBS, a KMS call is first performed to get the plaintext key for the data. Then the data is encrypted and sent as cypher text to the EBS volume.
- When an EC2 instance is terminated, or when it changes hosts, the keys are discarded
- For that instance to use the volume again, the instance needs to decrypt the DEK and store the plaintext version in memory of another EC2 host.
- If a snapshot is created, it uses the same keys and 
- **NO COSTS - should be used by default**
- Accounts can be set to encrypt by default - **default KMS Key**
- Otherwise **choose a KMS Key** to use
- **EACH VOLUME USES 1 UNIQUE DEK**
- **CANNOT REMOVE ENCRYPTION FROM AN ENCRYPTED VOLUME**
- OS isn't aware of the encryption -> no performance loss on the instance


## Network Interfaces, instance IPs and DNS

- An EC2 instance has at least one ENI (Elastic Network Interfaces)
- We can have multiple ENIs (in different subnets), but the subnets have to be in the same AZ.

Network interfaces have:
	- a mac address
	- primary IPv4 private address
	- 0 or more secondary IPs
	- 0 or 1 public IPv4 Address
	- 1 elastic IP per private IPv4 address
	- 0 or more IPv6 addresses (already publicly routable)
	- Security Groups
	- Enable or Disable Source/Destination Checks

*For secondary network interfaces the options are the same as above BUT these can be detached and reattached to another EC2 Instances.*

**If we have a public IPv4 address allocated, inside the VPC it will resolve to the primary private IPv4 address, but if we are outside the VPC, it resolves to the public IP**

**If we enable the elastic IP it removes the public IPv4 address, and it gets replaced with the elastic one!**

Secondary ENI + MAC = **Licensing**
Multiple interfaces can be used for multi-homed (subnets) Management & Data
Different Security Groups with different types of access -> multiple interfaces!



## Amazon Machine Images (AMI)

- Like Docker images but for EC2 instances
- **AWS or community** provided
- Regional and with **uniqueID** (e.g ami-786g89fsd7822f)
- Marketplace (can include commercial software)

**AMI Lifecycle**
![[Screenshot 2024-04-01 at 17.44.42.png]]



- AMIs are created in One Region and can be used only in that region
- AMI Baking -> Creating an AMI from an existing instance + application
- **An AMI can't be edited** -> launch, configure, and create a new one
- Can be copied between regions (including it's snapshots)
- By default, an AMI is only available to the owners account
- An AMI has EBS snapshots and we will be billed for the capacity of those snapshots
- BEST PRACTICES FOR AMI SHARE: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/sharing-amis.html
- 

























=======
- to mount it automatically at startup, the volume UUID must be set in the "fstab" config file
- SNAPSHOTS cannot only be used to transfer volumes from one AZ to another, but we can also copy them to different regions

- **Instance Store Volumes**  (ones attached to the instance by default and on-premise) **persist data over reboots** (*not start and stop, but reboot*).  unless configured to do so!
- On **START AND STOP** the instance may move to another EC2 Host! In this scenario, the file system might be persisted, but the data is lost :/ 

## EC2 Network and DNS Architecture

From a networking perspective, every EC2 instance that is created receives a network interface (called the primary network interface). Multiple ENIs (elastic network interfaces) can be created for that instance afterwards.

Each network interface has the following characteristics:
![[Pasted image 20240628011926.png]]

**!!!! When we assign an Elastic IP Address, the previous private IP address is discarded and replaced by the elastic one. When the elastic ip is deactivated, ANOTHER private ip is created!!!!

**!!!! Back in the day, a lot of legacy code was licensed using a MAC address (because it is static). We can use a secondary elastic network interface and it's mac address for licensing, and if we want to move the license to another EC2 instance, we can do that by detaching the ENI from the current ec2 instance, and attach it to another one that will be licensed.

**!!!! Can have multiple ENIs with different security groups ( as SGs are created per ENI)

**!!!! The OS doesn't see the public IPv4 !!! 

**!!!! The public DNS (which is given to the VPC for the public ipv4 address) will resolve to the private IP if accessed from within the VPC, and to the public one if accessed from the internet

## EC2 Purchase Options

### 1. **On-Demand Instances**

- **Description**: Pay for compute capacity by the hour or second with no long-term commitments.
- **Benefits**:
    - No upfront costs or long-term commitments.
    - Flexibility to increase or decrease compute capacity depending on the demands of your application.
    - Ideal for short-term, irregular workloads that cannot be interrupted.
- **Use Cases**:
    - Development and testing environments.
    - Applications with unpredictable workloads.
    - Applications being developed or tested for the first time.

### 2. **Reserved Instances (RIs)**

- **Description**: Commit to using EC2 instances for a 1- or 3-year term to receive a significant discount compared to On-Demand pricing.
- **Types**:
    - **Standard Reserved Instances**: Offer up to 75% discount and are best for steady-state usage.
    - **Convertible Reserved Instances**: Allow you to change the instance type and offer up to 54% discount.
- **Benefits**:
    - Significant cost savings for long-term commitments.
    - Capacity reservation when needed (for standard RIs).
- **Use Cases**:
    - Applications with predictable usage that run continuously.
    - Enterprises looking to save costs on long-term applications.

### 3. **Savings Plans**

- **Description**: Flexible pricing model offering lower prices on EC2, Lambda and Fargate usage in exchange for a commitment to a consistent amount of usage (measured in $/hour) for 1 or 3 years.
- **Types**:
    - **Compute Savings Plans**: Apply to any EC2 instance regardless of region, instance family, operating system, or tenancy.
    - **EC2 Instance Savings Plans**: Apply to specific instance families within a region.
- **Benefits**:
    - Greater flexibility than Reserved Instances.
    - Up to 72% savings compared to On-Demand prices.
- **Use Cases**:
    - Workloads with predictable usage patterns.
    - Organizations looking for flexible saving options across different compute services.

### 4. **Spot Instances**

- **Description**: Purchase spare EC2 capacity at significantly reduced rates (up to 90% off On-Demand prices).
- **Benefits**:
    - Extremely cost-effective for workloads that can tolerate interruptions.
    - Ideal for batch processing, big data analytics, and fault-tolerant workloads.
- **Use Cases**:
    - Data analysis, batch jobs, and background processing.
    - Stateless or loosely coupled workloads.
    - CI/CD workloads that can handle interruptions.

### 5. **Dedicated Hosts**

- **Description**: Physical servers dedicated to your use, allowing you to use your existing software licenses.
- **Benefits**:
    - Compliance and regulatory requirements.
    - Use of existing server-bound software licenses.
    - Better control over instance placement.
- **Use Cases**:
    - Highly regulated industries (e.g., healthcare, finance).
    - Applications that require a physical server with dedicated resources.
    - Licensing requirements that do not support multi-tenancy.

### 6. **Dedicated Instances**

- **Description**: Instances that run on hardware dedicated to a single customer, isolating them from instances of other customers.
- **Benefits**:
    - Isolation at the hardware level.
    - Meet compliance requirements without needing a Dedicated Host.
- **Use Cases**:
    - Workloads requiring additional isolation.
    - Organizations with strict compliance requirements.

### 7. **Capacity Reservations**

- **Description**: Reserve capacity in a specific Availability Zone for any duration.
- **Benefits**:
    - Ensures you have the capacity when you need it.
    - Can be combined with Reserved Instances for cost savings.
- **Use Cases**:
    - Applications with urgent capacity needs.
    - Ensuring capacity for future scaling needs.



## Status Checks and Auto Recovery

Each instance has 2 status checks:
1. for things like loss of system power, network, software and hardware issues
2. for instance related stuff like corrupt file systems, OS Kernel issues or incorrect networking

Auto recovery moves the instance to a new host with the same config as before ( all IP addressing is maintained )

Status checks can be performed periodically and in case of failures they can trigger alarms.

For termination protection we need to modify the 'disableApiTermination' attribute.


# Next Chapter : [[Chapter 7 - Monitoring and Logging]]

#AWS_Learning 

