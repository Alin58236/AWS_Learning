
- ## IAM Identity Policies
	- created using an IAM Document (JSON)
	- Contains statements
	- Can specify policies such as "Allow", "Deny", etc.
	- Can specify access to resources i.e. "Allow access to a certain folder in S3"
	- ![[Pasted image 20240223181911.png]]
		- ###### this is a policy that has 2 statements:
			1. Allow full access to all resources
			2. Deny access to catgifs bucket in S3
	- ***TIP:*** 
		  **Q:** *HERE WE HAVE A POLICY THAT ALLOWS AND ALSO DENIES ACCESS. WHICH TAKES PLACE FIRST?*
		  **A:** *EXPLICIT DENY  > EXPLICIT ALLOW > DEFAULT DENY*

	- *If a person has 2 policies, and it's part of a group (i.e. Developers Group) that has it's own policy, and tries to access a resource that has it's own different policy, when the person makes the request for that resource, all policies are collected and evaluated at the same time!*
	
	- It is **NOT** a good practice to give the **same individual policy (inline policy) to multiple individuals** that need the same access ( *hard to manage if there are 400 persons that need that access, or even worse, that need the same access changes*). A solution would be to create a **MANAGED POLICY** (which is an object on it's own) and give it to multiple persons (change in only one place).
	- **MANAGED POLICIES should be used for the default policies in an org**
	- **INLINE POLICIES should be used for custom, exceptional policies for identities**

- ## IAM Users and ARNs
	- ARNs (Amazon Resource Names) are used to uniquely identify any AWS resource within an AWS Account
	- The ARN format is **arn: partition : service : region : account-id:resource-id**
	- ARNs can also be wildcards i.e. ![[Pasted image 20240223201528.png]]
		
	- **The second ARN doesn't identify the bucket, but all the objects in the bucket!**
	- Can have 5000 IAM users / account
	- IAM user can be a member of maximum 10 IAM Groups


- ## Simple Identity Permissions
	
	- 















#AWS_Learning 