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

## NOTE! Connection through SSH vs EC2 Instance Connect
## Storage

#### There are different types of storage:
1. **Direct (local) attached storage** - Storage on the EC2 Host
2. **Network attached storage** - EBS - delivered over the network
3. **Ephemeral Storage** - temporary storage
4. **Persistent Storage** - IS PERMANENT - outlives the EC2 Instance lifetime

#### Categories of Storage:
1. **Block Storage** - Volume presented to the OS as a collection of blocks 
  -  No Structure 
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

- **Block Storage** - raw disk allocation (volume) - can be **encrypted** using **KMS**
- instances see block device and create file system on this device (ext3/4, xfs)
- Storage is provisioned in **ONE AZ!!!**
- attached to **one** EC2 instance over a storage network
- can be **DETACHED** and **REATTACHED**, not lifecycle linked to one instance 
- If an instance moves from one host to another, the EBS follows.. -> persistent
- **BACKUP IN S3** to migrate between **AZs**
- *different storage types, different sizes, different performance profiles*
- billed on GB/month

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
- High-Performance -> it depends but usually*** Instance Store***
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
- to mount it automatically at startup, the volume UUID must be set in the fstab config file
- SNAPSHOTS cannot only be used to transfer volumes from one AZ to another, but we can also copy them to different regions