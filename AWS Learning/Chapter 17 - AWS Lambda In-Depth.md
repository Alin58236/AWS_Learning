
### Lambda Function Handler

- lambda has lifecycles
	- ***INIT*** - the execution environment gets created or unfreezed
	- ***INVOKE*** - Cold start for the function handler
	- next ***INVOKE***s - warm start (faster than cold start if it happens immediately after)
	- ***SHUTDOWN*** - Terminate the environment

![[Screenshot 2025-07-30 at 12.37.23.png]]

When we run the warm start only the content of the lambda_handler is run.


### Lambda Versions

- Unpublished function - can be changed or deployed
- the latest version deploys to ***$LATEST***

- Functions can be published - creates an immutable version
	- locked , no editing of that published version


- A unique ARN for that function version
- ***Qualified ARN*** points at a specific version

![[Screenshot 2025-07-30 at 15.48.12.png]]



### Lambda Aliases

- An alias is a pointer to a ***function version***
- we might create an alias called "PROD" which points to function_name:1, and "BETA" which points to function_name:2

- after testing on beta is done, alias for PROD can be updated to point at version:2

- We can also route traffic : a percentage on v1 and another percentage at v2

### Lambda Environment Variables

- Key/Value pairs
- Associated with $LATEST
- associated with a version (immutable / fixed)
- can be accessed within the execution environment
- can be encrypted with KMS

### Monitoring, Logging and Tracing Lambda Functions

#### Logs

- metrics and telemetry data can be accessed both in CloudWatch, or by looking at metrics for specific function in the monitoring tab
- Dimensions - function name, resource (alias/version), and ALL FUNCTIONS
- Can review invocations, errors, duration, concurrent executions
- DeadLetterErrors, DestinationDeliveryFailures

- Lambda Execution Logs -> CloudWatch Logs
- stdout or stderr will be captured by logs
- Log groups = /aws/lambda/functions
- Log Stream = YYYY/MM/DD/[$LATEST || Version]..random
- Permissions via Execution Role (Default role gives logging permissions)


#### Tracing

- X-ray shows the flow of requests through your application
- enable "Active Tracing" on a fucntion
- `aws lambda update-function-configuration --functionName my-function --tracing-config Mode=active 
- AWSXRayDaemonWriteAccess managed policy
- Use X-Ray SDK within your function {AWS_XRAY_DAEMON_ADDRESS}


### Demo

![[Screenshot 2025-07-30 at 17.13.31.png]]




### Lambda Layers

- Without layers, everything is contained in one deployment zip (code + libraries)
- With layers we can split the function code (different for each function) from the library layer (and this layer can be shared by multiple functions)
- libraries are extracted in /opt folder
- Layers allow new runtimes and allow libraries to be externalised
- Deployment ZIPs are smaller with shared libraries reused between functions

ex. We have a function that uses scipy and numpy (big math libs)

one layer contains the libraries, and one contains the function

### Lambda Container Images

- wtf containers? wasn't it an anti-pattern? - yes it was

- But Orgs use containers and CI/CD processes are built for containers
- What we can do for Local Testing is to use the ***Lambda Runtime API*** in a **Container Image**

- AWS Lambda Runtime Interface Emulator (RIE) - for LOCAL TESTS

![[Screenshot 2025-07-30 at 17.45.18.png]]


### Lambda and ALB Integration

- we can integrate the Application Load Balancer to balance loads between multiple instances of a function
- multi value headers are arrays of query string parameters

- watch the lesson for demo.. not really a big deal

### Lambda Resource Policies

Execution roles are IAM roles that are assumed by the invoked functions - tells us **WHAT** can a function **DO**

Resource policy determines ***WHO*** can do ***WHAT*** with the lambda function

In the same account only an identity policy OR a resource policy is needed

Across accounts require Identity (OUT) and Resource (IN) policies

![[Screenshot 2025-07-31 at 01.45.45.png]]

- A lambda resource policy is required when a service needs to invoke lambda
- can view and perform limited edits in the UI
- also full control via the CLI/API






Next Chapter: [[Chapter 18 - API and API Gateway In-Depth]]

#AWS_Learning 