

AWS has public services as well as private services (depending on the IP addresses that they use).

- ## **AWS Global infrastructure** 

	Is classified in 2 types of deployments: 
	
	- **AWS Regions** (geographically spread - full compute, AI, Storage, Analytics, etc.)
			Using these regions we can create systems that can withstand **global level disasters**
			When you use a service such as an **S3 bucket**, you actually use an S3 inside a **certain region**.
	- **AWS Edge Location** (Local Distribution Points)
		     Used for when there are no close regions to the target, as there are a lot more edge locations than regions.
		     Useful for when we want to reduce the latency for clients in far areas.

		Most of the services are Region Dependent (S3, EC2), but there are a few services that act globally (IAM).
	
	**Advantages of this infrastructure:**
	- **Geographic separation** - Isolated Fault Domain
	- **Geopolitical Separation** - Different Governance (when we use EC2 in EU, we abide the laws of the EU)
	- **Location Control** - Performance

- ## **Regions and Availability Zones**
	-  Regions have a **Region Code (ap-southeast-2)** and a **Region Name** (**Asia Pacific (Sydney)**)
	- For every **Region**, AWS provides **multiple AZs**, and for every AZ, you are provided **isolated infrastructure (compute, network, storage, etc.) inside that region**.
	
		***e.g. if a power outage in AZ ap-southeast-2a affects the infrastructure of the services, ap-southeast-2b, and 2c will be still intact***

		Levels of resilience for Services:
		- **Global Resilience** (IAM, Route53): multiple regions can be affected without it being taken down.
		- **Region Resilience**: A service that has 2 different instances in 2 different AZs in the same region (implies data replication). If an AZ is affected, a service is affected.
		- **AZ Resilience**: A service that has one or more instance in the same AZ (if the AZ is affected, all services are down).

- ## Virtual Private Cloud (VPC) Basics

	 - A VPC is a virtual network inside the Cloud.
	 - A VPC operates in **multiple AZs inside** a **Region**
	 - **Private** and **Isolated** unless we decide otherwise
	 - Two types: **Default VPCs (only 1 per region)** and **Custom VPCs (many per region, 100% private by default)**

	- VPCs are created by an AWS Account in a **specific region**, and no traffic can cross their boundaries unless specifically configured. The VPCs are **Regionally Resilient**.
	- The VPC is allocated a range of IP Addresses called the **VPC CIRD (172.31.0.0/16)**
	- The Default VPC always has one subnet in every AZ (172.31.X.X/20) of the Region  we create it in. Always IPv4.
		![[Pasted image 20240210153508.png]]
	
	- **Every** VPC comes with an ***Internet Gateway (IGW), Security Group, and NACL*** implemented

- ## Elastic Compute Cloud (EC2) Basics

	- EC2 is **IAAS (Infrastructure-As-A-Service)** -> The unit of consumption is the EC2 Instance (a Virtual Machine)
	- EC2 is a private service (runs in the AWS Private Zone)
	- In order to give it public access we need a VPC that supports that
	- AZ Resilient (if the AZ fails, the instance fails)
	- Different instance sizes and capabilities
	- On-Demand Billing (per second) for CPU, RAM, DISK & Network
	- Local on-host storage or Elastic Block Storage (EBS)

		**Instance Lifecycle has 3 states**:
		- Running (charged for CPU, RAM, Disk, Network)
		- Stopped (No CPU, Memory, or Network is consumed, only Storage $)
		- Terminated (nothing is consumed, the disk is deleted, and the terminated instance is not reversible)

		**Amazon Machine Image (AMI)** - basically docker for AWS
		 The AMI Contains:
		- **Permissions**:
			- **Public** - everyone allowed
			- **Owner**- Implicit allow
			- **Explicit** - specific AWS accounts allowed
		- **Root Volume** (C:// in Windows or root in Linux)
		- **Block Device Mapping**
		
		**Connecting to EC2**
		- Through RDP (port 3389) on Windows using Private key and local admin password
		- Through SSH or PuTTY on Linux (port 22) using Private Key

- ## Simple Storage Service (S3) Basics ##

	- **Global** Storage Platform (**Regional Resilient**) -> **can withstand an AZ outage**
	- **Public service**, unlimited data & designed for **multi-user**
	- Perfect for hosting large amounts of data (**Movies, Audio, Photos, Text, Large Datasets**) and to be used as **INPUT** or **OUTPUT**
	- **Economical**
	- Accessed via **UI**/**CLI**/**API**/**Http**
	- It delivers 2 things:
		- **objects (files)**
		- **buckets (storage units / containers for objects)**

	- Every object has a **key (name)**, a **value (the content)** and a **bucket** where it is stored
	- the value can have **from 0 bytes up to 5 TB**
	- ![[Pasted image 20240216131210.png]]

	- Buckets are created **inside a region**
	- The data inside it never leaves the region unless configured to do so
	- **Bucket Names must be globally unique, 3-63 chars all lowercase & no underscore**, ***can't*** **be formatted as an IP, and has to begin with a letter or number**
	- No. of buckets created - 100 soft limit, and 1000 hard limit per account
	
	- ***To be noted that it is not a filesystem, the structure is flat and everything is stored at the root bucket level***

	- There is no concept of filetype (if we have a img4.jpeg, the **".jpeg"** is part of the name)
	- if there is a file called "something/img4.jpeg" the file will be displayed as being inside a folder called "*something*"


	- 

- ## CloudFormation (CFN) Basics
	
	 - Service for **resource management**
	 - uses CloudFormation **Templates** to create and delete resources (**YAML or JSON**)
	 - The **resources section is the only mandatory part** of the CloudFormation template
	 - The description field must directly follow the **AWSTemplateFormatVersion**
	 - The **metadata** field can control the UI groupings
	 - The **parameters** section is used to customize criteria for creation and behavior of resources
	 - **Mappings** is a section that allows us to create lookup tables
	 - **Conditions** allow decision making in the template

		![[Pasted image 20240220220454.png]]
			*A CloudFormation Template that creates an ec2 instance*
			
	- When you create a template, the CloudFormation creates a **"Stack"** which contains all the *logical resources* that you create
	- For any logical resource in the stack, CloudFormation creates an **actual physical instance (is CFNs job to keep the stack and the template in sync)**
	 ![[Pasted image 20240220221916.png]]

- ## CloudWatch (CW) Basics

	- used for ops management and monitoring
	- it collects and manages data
	- **Metrics** - AWS Products, Apps, On-Premises
		- Is a **public** service
		- **CloudWatch Agent** is needed in order to gather metrics outside of AWS
	- **CloudWatch Logs** - AWS Products, Apps, On-Premises
	- **CloudWatch Events** - AWS Services & Schedules

	![[Pasted image 20240221013829.png]]
						*CloudWatch Architecture*

	- **Namespaces** are containers for storing data related to metrics (keeps things from getting messy)
	- All metric data in AWS goes into a namespace called **AWS/service**
	- ***Metric = Time ordered set of data points***
	![[Pasted image 20240221014157.png]]

	- **Dimensions** - used to separate and filter datapoints in metrics

	![[Pasted image 20240221014356.png]]

	- **Alarms** - A metric state which has values ( OK or Alarm). For the latter, an action is triggered

- ## Shared Responsibility Model
		![[Pasted image 20240222011410.png]]

	- ***High Availability (HA)*** - **ensures** an agreed level of operational **performance**, usually **uptime**, for a **higher than normal period** (does not mean there's no faults, and it's not about user experience, but maximizing uptime)
	- ***Fault Tolerance (FT)*** - enables a system to **continue operating properly** in the event of the **failure of some** (1 or more) of it's **components**
	- ***Disaster Recovery (DT)*** a set of policies, tools and procedures to **enable the recovery** or **continuation** of vital technology infrastructure and systems **following a disaster**

- ## Route53 Basics

	- Provides 2 main services:
		- 1. **Register domains**
		- 2. **Host Zones.. Managed nameservers**
		
	- Is a global service, single database
	
	- Globally resilient
	- 
	 ##### DNS RECORD TYPES
	 - **NS Records** (.com, .org, etc.)
	 - **A Records** ( map host names to ipv4)
	 - **AAAA Records** ( map host names to ipv6)
	 - **CNAME Records** ( host -> host)
	 - **MX Records** (Host -> IP -> SMTP)
	 - **TXT Records** ( used for adding additional information to DNS Records i.e. proving that the DNS record is genuine)


#AWS_Learning
###### Next Chapter : [[Chapter 2 - IAM, ACCOUNTS AND AWS ORGANISATION]]