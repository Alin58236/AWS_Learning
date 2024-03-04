- ## S3 Security
	- private by default
	- the only identity that has access to the bucket is the Account ROOT user of the account that created that bucket
	
	- ### S3 Bucket policy
		- a form of **Resource Policy**
		- Like **identity policies, but attached to a bucket**
		- **Allow / Deny same or different accounts**
		- **Allow / Deny Anonymous principals**
		- Resource policies **have a specific field** (Principal: "*")
		- Bucket policies have conditions (through the Condition field)
			- ![[Pasted image 20240302194517.png]]***e.g. Allow access to bucket to all principals except the IP Address 1.3.3.7/32***
			
	- ### Access Control Lists (ACLs)
		- ACLs can be put on objects and bucket
		- Legacy stuff, not recommended by AWS
		- A subresource
		- Inflexible and simple permissions (*READ, WRITE, READ_ACP, WRITE_ACP, FULL_CONTROL*)
		
	- ### Block Public Access Settings
		- These apply to all public access, not intra cloud IAM identities!!!

- ## S3 Static Hosting
	
	- Allows access via **HTTP**
	- **Index** and **Error** documents are set to specific objects in S3
	- Website endpoint is created
	- Custom Domain via **R53** - **BUCKETNAME MATTERS**













