## Policy Evaluation

	- Explicit deny?
	- Organisation Service Control Policies? - impact what identities inside organisation member accounts can do
	- resource policies
	- IAM Identity permission boundaries
	- Session policies
	- Identity policies
	
	
- Flow for one account implied: ![[Screenshot 2024-03-15 at 21.42.10 1.png]]
- Flow for multiple accounts![[Screenshot 2024-03-15 at 21.45.29.png]]


## CloudHSM

- product similar to KMS - Key management
- The KMS product actually runs a HSM (Hardware security module), and an HSM can also be run on premise
- **KMS is a shared service, but separated, while HSM is a true "single tenant" HSM that is AWS provisioned, but fully customer managed**
- **As a security standard, HSM is Fully FIPs 140-2 L3 (KMS is L2, some L3)**
- We access HSM using industry standard APIs like :
	- **PCKS** #11
	- Java Cryptography Extensions (**JCE**)
	- Microsoft **CryptoNG** (**CNG**) 
- ***KMS can use CloudHSM as a custom Key Store***

### Configuration:

- **HSM is not deployed in a Customer Controlled VPC, but in a Cloud HSM VPC**
- **To make them highly available we need to deploy multiple HSMs in multiple Availability Zones**
- **They can be injected in our VPCs via Elastic Network Interfaces (ENI)**
- **If we want an EC2 to interact with the CloudHSM devices we need to install on it a CloudHSM Client**

### Use Cases & Limitations:

- *No native AWS Integrations (ex. Like S3 SSE)*
- Can be used to **offload SSL/TLS processing** for web servers
- Performs **Transparent Data Encryption (TDE)** for Oracle Databases (*AWS has no ability to decrypt the data, good if you're using it in a high regulatory environment*)
- Protect private Keys for an Issuing Certificate Authority (CA) -> *If we are running our own certificate authority we can use CloudHSM to protect our Keys*

*! useful for anything that is not AWS related*

#AWS_Learning
## Next Chapter: [[Chapter 5 - Virtual Private Cloud (VPCs)]]




