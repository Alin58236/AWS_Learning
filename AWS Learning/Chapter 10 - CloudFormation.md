
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

NEXT -> CloudFromation : Template and Pseudo Parameters