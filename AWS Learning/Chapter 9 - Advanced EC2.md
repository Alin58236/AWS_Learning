
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

!! Better use cloudFormation!


### EC2 Instance Roles

- Until now we've used IAM Roles to grant services the access that they need in order to interact with other AWS services or features. 

- For EC2 instances things are pretty similar, but here we will be using **Instance Profiles** to assign roles to instances
	- The *Instance Profile* is a wrapper over an **IAM Role** containing access policies, and it is the actual component that is associated to an instance so that it can assume a role. 
	- When we were creating an instance and we used to assign it an IAM Role, what was happening under the hood was: AWS was creating an instance profile with the same name as the IAM Role, and it was assigning this profile to the EC2 Instance, **BUT when we're using CloudFormation to create and manage EC2 instances, these 2 must be created manually and separately**
- EC2 Instance roles always use instance meta-data that are automatically rotated (always valid)
	- ( iam/security-credentials/credentials/**role-name** )
- It is a better alternative to adding security/access keys into an instance

![[Screenshot 2024-10-06 at 18.40.22.png]]



TODO // SSM Parameter Store!!!