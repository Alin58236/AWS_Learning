## VPC Considerations for planning

- What size should it be?
- Are there any networks we can't use? 
	
	- *(For example, in Production, the Azure Cloud uses the same IP interval as the AWS Default VPC, which is 172.31.0.0/16 = 172.31.0.0 -> 172.31.255.255)*
	
- We can't use ranges of other VPCs, Cloud, On Premises, Partners & Vendors
- Try to predict the future
- VPC Structure - Tiers & Resiliency (Availability) Zones
- **VPC Minimum size is /28 (16 IP Addresses), VPC Maximum size is /16 (65536 IP Addresses)**

- example VPC for Animals4Life Business Scenario
	  ![[Screenshot 2024-03-16 at 13.28.21.png]]

- Sizes of VPCs ![[Screenshot 2024-03-16 at 13.30.51.png]]
- **We cannot simply launch services in VPCs! We need to create subnets inside the VPCs (and subnets stay in an Availability Zone)**
- VPC Structure ![[Screenshot 2024-03-16 at 13.35.48.png]]
		- *good practice to choose 4 AZs (3 + spare)*
		- *good practice to choose 4 layers (3 + spare)*
		- *If the VPC is /16 Range, the subnets will be /20 range (16+4 layers/AZ = /20)*![[Screenshot 2024-03-16 at 13.46.39.png]]



## Custom VPCs
 - Regionally isolated (All AZs in the region)
 - Nothing IN or OUT without explicit config
 - Flexible config (simple or multi-tier)
 - Hybrid networking supported (on premises or other cloud)
 - Default or Dedicated Tenancy
 - Default IPv4 - min. /28 max. /16
 - Optional single assigned IPv6 /56 CIDR block
 - IPv6 are all publicly accessible by default
 - AWS VPCs have fully-featured DNS provided by Route53
 - "enableDnsHostnames" - gives instances DNS names
 - "enableDnsSupport" - enables DNS resolution in VPC

## VPC Subnets

	- there are public and private subnets
	- AZ resilient feature
	- a subnetwork of a VPC - within a particular AZ
	- a subnet cannot be in more availability zones
	- the IPv4 CIRD has to be within the range of the VPC
	
	Example - 5 IPs are restricted:
	for 10.16.16.0/20 ( 10.16.16.0 - 10.16.31.255)
	- Network Address: 10.16.16.0
	- VPC Router (Network + 1) : 10.16.16.1
	- Reserved (DNS*, Network + 2) : 10.16.16.2
	- Reserved Future Use ( Network + 3 ) : 10.16.16.3
	- Last IP in subnet ( for broadcast ) : 10.16.31.255

	Every VPC has an DHCP option set. They can be created but cannot be edited. They can Auto Assign IPv4 Addresses.


## VPC Routing, Internet Gateway and Bastion

- Every VPC has a VPC router - highly available
- In every subnet - Network+1 Address
- routes traffic between subnets
- Controlled by route tables - every subnet has one
- **A VPC has a MAIN route table - if it's not explicitly defined, the subnet default is used**

### Internet Gateway (IGW)

- Region Resilient gateway attached to a VPC
- runs form within the AWS Public Zone
- Gateways traffic between the VPC and the AWS Public Zone (S3, SQS, SNS, etc.)
- Managed - AWS Handles Performance

**Steps for implementation :**
1. Create IGW
2. Attach IGW to VPC
3. Create custom Route Table 
4. Associate Route Table
5. Default Routes => IGW
6. Subnet Allocate IPv4
























### Bastion Host

- Bastion Host = Jumpbox
- An instance in a public subnet
- Incoming management connections arrive there
- Then access internal VPC resources
- Often the only way IN a VPC




## Network Access Control Lists (NACLs)

- **NACLs are stateless** (don't know if a packet of data is part of a request, or if it is a response)
- NACLs contain rules grouped into ***INBOUND*** and ***OUTBOUND***
- Rules match destination IP, port, range, protocol, and ***ALLOW*** or ***DENY*** based on that match
- Rules are processed in order, lowest rule number first. Once a match occurs, processing ***STOPS***
- "*" is an implicit deny if nothing else matches
- NACLs are created for a specific VPC and are initially associated with no subnets
- Cannot be associated to AWS resources - only subnets
- Used together with security groups to add explicit deny (bad IPs/Nets)
- **Each subnet can have one NACL**
- ***One NACL can be associated to many subnets***

## Security Groups (SG)

- **Stateful** - detect response traffic automatically
- Allowed request => allowed response
- **LIMITATION: No explicit DENY**
- Can't block specific bad actors
- Supports IP/CIDR and logical resources (including other **SGs** and **ITSELF**)
- The self-referencing is useful if we have multiple instances of EC2 that need to communicate with each other
- IP changes are automatically handled


## Network Address Translation (NAT) & NAT Gateway

- set of processes - remapping SRC or DST IPs
- IP Masking - hiding CIDR blocks behind one IP
- Public IPv4 Addresses running out
- Gives private CIDR range *outgoing* internet access

- Can be used in 2 ways:
	- An EC2 Instance configured to provide NAT
	- A NAT Gateway

- Architecture![[Screenshot 2024-03-20 at 16.45.18.png]]

#### - If we need to give an instance it's own public IPv4 address then ONLY the Internet Gateway is required. If we want to give private instances outgoing access to the public internet then we need both a NAT Gateway and an IGW (to give the NAT translated address a public IPv4 address)

#### - NAT gateway needs to be in a public subnet

#### - Uses what is called an Elastic IP (static)

#### - AZ resilient service

#### - for region resilience - one NAT GW in each AZ and one Route Table for each AZ with that NAT GW as target

- Managed, scales to 45 Gbps, $ duration(hourly charge) & Data Volume
- ![[Screenshot 2024-03-22 at 00.21.38.png]]

------ Mai repeta partea de NAT si de /ip-range







## Related: [[Networking Basics]]


# Next Chapter : [[Chapter 6 - Elastic Compute Cloud (EC2)]]

#AWS_Learning 

