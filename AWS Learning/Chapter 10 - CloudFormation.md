
### Example 1 - Simple CloudFormation template - Non Portable

```
Resources:
	Bucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      BucketName: 'accatpics13333337'
  Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: "t2.micro"
      ImageId: 'ami-090fa75af13c156b4'
```

S3 name is explicitly typed in our template -> non reusable template as S3 buckets cannot have the same name

Same for the AMI id if we switch the AWS Region!

### Template and Pseudo Parameters

#### Template Params

- the template params accept input - console/CLI/API
- Can be referenced from logical resources
- influence physical resources
- configured with Defaults, AllowddValues, Min, Max, AllowedPatterns, NoEcho & Type

#### Pseudo Params

Are similar to template params only are provided by aws based in the environment when creating the stack!


### CloudFormation Intrinsic Functions


AWS CloudFormation provides several built-in functions that help you manage your stacks. Use intrinsic functions in your templates to assign values to properties that are not available until runtime.

You can use intrinsic functions only in specific parts of a template. Currently, you can use intrinsic functions in resource properties, outputs, metadata attributes, and update policy attributes. You can also use intrinsic functions to conditionally create stack resources.

###### Functions:

- [Fn::Base64](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-base64.html)
- [Fn::Cidr](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-cidr.html)
- [Condition functions](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-conditions.html)
- [Fn::FindInMap](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-findinmap.html)
- [Fn::ForEach](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-foreach.html)
- [Fn::GetAtt](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-getatt.html)
- [Fn::GetAZs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-getavailabilityzones.html)
- [Fn::ImportValue](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-importvalue.html)
- [Fn::Join](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-join.html)
- [Fn::Length](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-length.html)
- [Fn::Select](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-select.html)
- [Fn::Split](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-split.html)
- [Fn::Sub](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-sub.html)
- [Fn::ToJsonString](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-ToJsonString.html)
- [Fn::Transform](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-transform.html)
- [Ref](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-ref.html)


### CloudFormation Mappings

- Templates can contain a **Mappings** Object
- Maps keys to values
- Can use !FindInMap Intrinsic function
- Used to improve template portability
- Mapping Example 

 
![[Screenshot 2025-02-15 at 19.17.02.png]] 

### Outputs

- Templates can have an optional output section
- makes outputs visible in the CLI and ConsoleUI
- Accessible from a parent stack when using nesting

### Conditions

- **AND, EQUALS, IF, NOT, OR**
- associated with logical resources to control if they are **created** or **not**
- Can create custom conditions (ex. ONEAZ, TWOAZ, THREEAZ - translated to 'create the resources in 1 AZ, 2, or 3')



![[Screenshot 2025-02-26 at 17.18.54.png]]

### DependsOn

A special condition that creates formal dependencies between logical resources in CF

CF can do things in parallel, so we use this to determine a custom dependency order

ex. **VPC -> SUBNET -> EC2**

if A and B depend on C, CF will wait for C to be created before creating A and B

With **DependsOn** we can specify a single resource, or a list of resources


### Wait Conditions and cfn-signal

The cfn-signal can configure CF to 'hold', wait for x number of success signals, or set a timeout

If failure signal is received instead of a success one, the creation fails

if timeout is reached, the creation fails

This is achieved by using a resource called "CreationPolicy", or a "WaitCondition"

#### Creation Policy

![[Screenshot 2025-02-26 at 17.53.01.png]]
- it uses signals

#### Wait Condition

- it can depend on other resources
- uses a WaitHandle ( another resource )

![[Screenshot 2025-02-26 at 17.56.31.png]]

### Nested Stacks

- Stacks have resource limits (500) -> problem for large deployments

- Can't easily reference other stacks, as they are isolated
- To reference we can use Nested Stacks or Cross-Stack-Referencing
- ![[Screenshot 2025-03-27 at 15.50.21.png]]

**These should be used only when everything is lifecycle linked**


### Cross Reference Stacks

- CFN Stacks are designed to be isolated and self-contained
- In Cross-Stack References we can use (eg.) in Stack A the same VPC as Stack B
- Outputs are normally not visible from other stacks (so we can't use Stack A outputs as Stack B Input Parameter). 
- On the other side **Outputs can be exported** -> making them visible to other Stacks
- Exports must have a unique name in the region
- `Fn::ImportValue` can be used instead of `Ref`

 ![[Screenshot 2025-03-27 at 16.06.24.png]]



### Stack Sets

- Deploy CFN stacks across many **accounts** and **regions**
- Stack sets are ***containers*** in an **admin account**
- These Contain Stack instances which reference stacks
- each stack is in ***ONE REGION & ONE ACCOUNT***
- security = self-managed roles or service-managed roles

![[Screenshot 2025-03-27 at 17.09.48.png]]

The stacks have the following params:
1. Concurrent Accounts
2. Failure Tolerance
3. Retain Stacks


### Deletion Policies

If you delete a logical resource from a template -> by default, the **physical resource** is deleted

This can cause data loss!

With Deletion policies we can define if we want to delete (Default), Retain, or Snapshot

Snapshots continue to exist past Stack lifetime -> costs -> we have to clean them up

Only applies to **DELETE**, not **REPLACE**

### CFN Stack Roles

- When you create a stack, CFN uses the permissions of the logged in identity
- This means that you need permissions for AWS
- CFN can assume a role to gain the permissions

-- Demo - wait conditions, cfnsignal, cfninit


### CFN Change Sets

- Template -> Stack -> Physical Resources (***CREATION***)
- Stack (***DELETE***) -> Deletion of Physical Resources
- v2 Template -> Existing Stack -> Resources ***Change***

***No*** Interruption , ***Some*** Interruption (eg. Reboot of EC2) ,  **Replacement**

Change Sets allow us to apply new Templates to a stack, but instead of creating the immediately, it creates a Change set that we can overview.

![[Screenshot 2025-03-29 at 14.13.49.png]]

### CFN Custom Resources

Custom resources enable you to write custom provisioning logic in templates that AWS CloudFormation runs anytime you create, update (if you changed the custom resource), or delete stacks
eg. Populate an s3 bucket after creation, or delete its contents before deletion

### Quiz

1. What feature of cloudformation would you use to extend its functionality or integrate it with other system? -- **Custom Resources**
2. You're designing a system using CloudFormation which has two distinct parts. Infrastructure (which includes a VPC , subnets, gateways and configuration) and multiple application instances. How should you design this using stack architecture? -- **Stack Exports/Imports ( Cross Stack Reference )**
3. What feature of CloudFormation allows identities to deploy infrastructure in a controlled way, beyond their usual permissions? -- **Stack Roles**
4. What feature of CloudFormation Is often used to improve portability and make a template able to adjust itself based on where it's applied? -- **Intrinsic Functions**
5. Which feature of CloudFormation allows EC2 instances to update their configuration if a STACK changes? -- **CFN-HUP**
6. Which feature of CloudFormation allows an instance to tell CloudFormation when it's finished bootstrapping and configuration? -- **CFN-SIGNAL**
7. Which feature of CloudFormation allows it to be integrated into a Organisations Change management processes? -- **Change Sets**
8. If you have two stacks which are always applied together (e.g VPC stack and App Stack) which feature of CloudFormation should you use? -- **Nested Stacks**
9. If you need to deploy Infrastructure to multiple regions and accounts what CloudFormation feature should you use? -- **Stack Sets**
10. Which feature of CloudFormation allows you to influence the order of resources created by CFN? -- **DependsOn**


Next Chapter: [[Chapter 11 - Global Service Discovery and Content Delivery (R53 and CloudFront)]]

#AWS_Learning 