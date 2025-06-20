

- EFS is an AWS implementation of the NFSv4 (Network File Systems)
- EFS file systems can be mounted on linux
- Shared between MANY EC2 instances
- It exists separate from the EC2 instance (so it makes the EC2 instance kinda stateless) - just like EBS
- EBS is block storage while EFS is File Storage
- Private Service, via mount targets inside a VPC
- Can be accessed from on-prem - either using **VPN** or **DX**

#### Architecture

We create a filesystem (EFS) in a vpc, and it creates **Mount Targets** in different AZs (if configured), which take the IPs from the IP range of the VPC.

The Mount Targets are used by instances to access the filesystem

![[Screenshot 2025-06-20 at 18.25.45.png]]

- Linux Only
- ***General Purpose*** and ***MAX I/O*** modes
- General purpose is suited for 99% of scenarios
- ***Bursting*** and ***Provisioned*** throughput modes
- Storage Classes:
	1. Standard
	2. Infrequent Access

- Lifecycle policies can be used for this classes


### FSx for Windows File Server

- is one of the core components that AWS provides to support Windows Envs
- The previous EFS was only for Linux, this is like a Windows FileShares
- Native Windows file system
- Single or MultiAZ
- On demand or scheduled Backups
- Accessible using VPN, VPC, peering, Direct Connect

![[Screenshot 2025-06-20 at 19.51.07.png]]


### FSx for Lustre

Designed for various high performance computing workloads

It delivers extreme performance for scenarios such as Big Data, Machine Learning and Financial Modeling

Supports POSIX

- 100GB/s throughput and sub-millisecond latency
- Scratch - Short Term no replication and fast
- Persistent - Long term, High Availability (in one AZ), self-healing
- Accessible over VPN or Direct Connect

![[Screenshot 2025-06-20 at 20.22.12.png]]


![[Screenshot 2025-06-20 at 20.23.10.png]]

#### Reading and writing 

![[Screenshot 2025-06-20 at 20.25.37.png]]


Key 

![[Screenshot 2025-06-20 at 20.26.30.png]]


Next Chapter: [[Chapter 14 - Scaling, Load Balancing & High-Availability]]

#AWS_Learning 
