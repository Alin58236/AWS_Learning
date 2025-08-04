
### Architecture

- Platform as a Service
- **Developer Focused** - not end user
- great for small developer teams
- High Level - Managed Application Environments
- User Provides Code and EB handles environment
- Low infrastructure overhead
- Fully customisable - uses AWS Products under the hood
- Requires app changes -> doesn't come for free


It supports:

- built-in languages, Docker and custom platforms
- Go, Java SE, Tomcat
- .NET CORE ( Linux ) & .NET (Windows)
- Node.js, PHP, Ruby and Python
- Singlecontainer Docker or Multicontainer Docker
- pre configured Docker (what beanstalk uses)
- Custom via Packer


The application that you develop (the whole environment) in EB is called a container

Each container can have one or more environments (e.g. DEV, QA, PROD)

Each environment can be either a ***Web Server tier*** or a **Worker tier**

Worker tiers scale, based on the ***SQS messages*** they receive from the **Web Server tiers**

The web tier scales based on the requests that reach the web server.

- 2 environments can change their CNAME - can exchange 2 environment DNS (for hot swapping between QA and PROD for example -> for blue/green deployments )

![[Screenshot 2025-08-04 at 17.17.23.png]]
![[Screenshot 2025-08-04 at 17.43.42.png]]

- DBs can also be created in the beanstalk or applications in EB can point to external ones
- DBs in an env are lost when the env is deleted


### Deployment Policies

AWS Elastic Beanstalk provides several options for how deployments are processed, including deployment policies (_All at once_, _Rolling_, _Rolling with additional batch_, _Immutable_, and _Traffic splitting_) and options that let you configure batch size and health check behaviour during deployments

They determine how application versions are deployed to environments

***All at once*** - deploy to all at once, brief outage

![[Screenshot 2025-08-04 at 17.39.34.png]]

***Rolling*** - deploy in rolling batches - no increasing cost as the capacity is maintained

![[Screenshot 2025-08-04 at 17.40.19.png]]

***Rolling with additional batch*** - as above, but with new batch to maintain capacity during the process -> adds cost

![[Screenshot 2025-08-04 at 17.41.28.png]]

***Immutable*** - all new instances with new version - highest cost

![[Screenshot 2025-08-04 at 17.42.20.png]]

***Traffic Splitting*** - a new autoscaling group is created for the new fresh instances, with a traffic split until the old instances are terminated

![[Screenshot 2025-08-04 at 17.43.15.png]]

### Environments and RDS

There are 2 ways to use DBs in EB:

- we can create the DB in the environments
	- that makes the DB linked to the env
	- delete the env = delete RDS = data loss
	- different envs = different DBs = different data

- we can create DB outside of EB
	- add environment props to point at RDS instance:
		1. RDS_HOSTNAME
		2. RDS_PORT
		3. RDS_DB_NAME
		4. RDS_USERNAME
		5. RDS_PASSWORD
	- Environments can be changed at will - data is outside of the EB lifecycle
	- More decoupled 
	- to decouple we need to: 
		1. take a snapshot of the EB DB 
		2. "Enable Delete Protection"
		3. create a new EB environment with the same app version 
		4. ensure new env can connect to RDS 
		5. swap environments
		6. terminate old env
		7. locate DELETE_FAILED stack, manually delete and pick to retain stuck resources


### Advanced customisations via .ebextensions

- EB uses CloudFormation
- ebextensions are a way to customise EB environments
- inside application source bundle
- .ebextensions folder
- add YAML or JSON files ending with .config
- uses CFN format to create additional resources within the environment
- option_settings allows you to set options of resources
- resources allows entirely new resources
- ... packages, sources, files, users, groups, commands, container_commands, and services


### HTTPS

- to use https with EB we need to apply an SSL Certificate to the Load Balancer (either directly or using the console load balancer configuration)
- make sure you configure the security group

### Cloning

- creates a new EB environment by cloning an existing one
- copy PROD-ENV to a new TEST-ENV (for testing)
- new version of platform branch
- copies options, env variables, resources and settings
- ****includes RDS in env, but no data is copied!!!!!****
- "unmanaged" changes are not included

### Docker


- using Docker containers allows for extra functionality


#### **Single container per host:**

- This mode uses EC2, not ECS
- provide one of three things:
	1. Dockerfile
	2. Dockerrun.aws.json
	3. Docker-compose.yml

#### **Multi Container per host:**

- multiple container applications
- creates an ECS cluster
- Ec2 instances are provisioned in the Cluster and an ELB for high availability
- you need to provide Dockerrun.aws.json file in the application source bundle (root level)

![[Screenshot 2025-08-04 at 18.04.28.png]]

