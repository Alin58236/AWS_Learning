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

- ## Object Versioning & MFA Delete
	
	- ### Object versioning
		- **disabled by default**
		- **once enabled it can't be disabled**
		- an alternative is to **suspend** the bucket (if desired it can be re-enabled)
		
		- it lets you store **multiple versions of an object** in the **same bucket**
		- if you modify the object, it **generates a new version of that object**, leaving the original one in place
		- when the versioning is disabled the **ID** of the object is set to **NULL**
		- the **greatest ID value represents the latest / current version** (returned by default)
		
		- versioning also impacts **deletions**
		- if we want to delete an object and **we do not specify any ID**, the bucket will assume that we want to delete the **latest object version, along with all the previous versions**
		- the deletion is **not actually happening**, but a new version of that object, called a **DELETE MARKER**, is created, and it makes **all the versions under it look hidden**
		- If we delete the **DELETE MARKER the files are visible again**
		- In order to actually delete an object we have to specify the version ID
		- When the latest object is deleted, the previous version becomes the latest/current one
		- **!!!! STORAGE IS CONSUMED / BILLED FOR ALL VERSIONS OF THE OBJECT**
		- Only way to 0 costs is to delete the bucket
		
	- ### MFA Delete
		- **enabled in versioning config**
		- is required to **change bucket versioning state**
		- is required to **delete versions**
		- **in order to delete a version of an object we need to provide the serial number (MFA) + the Code Passed with API Calls**
		
	- ## S3 Performance Optimisation
		- By default, when you upload an object to S3 it is uploaded as **a huge blob in a single data stream**. The file becomes an object that will be uploaded using the PUT API













