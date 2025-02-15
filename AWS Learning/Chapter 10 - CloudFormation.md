
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


 