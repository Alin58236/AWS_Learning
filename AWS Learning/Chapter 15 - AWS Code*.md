### AWS CodePipeline


![[Screenshot 2025-07-21 at 11.35.09.png]]


### AWS CodeBuild


![[Screenshot 2025-07-21 at 11.36.20.png]]


![[Screenshot 2025-07-21 at 11.36.37.png]]

### AWS CodeDeploy

![[Screenshot 2025-07-21 at 11.37.15.png]]


#### Stages in the pipeline


![[Screenshot 2025-07-21 at 11.39.26.png]]



### ECR (Elastic Container Registry)


- Managed Container Image registry service (Analog la Docker Hub)

- Each AWS Account has a ***public*** and a ***private*** Registry

- Each registry can have multiple repositories

- Each repository can have multiple images

- Each Image can have multiple tags

- For public repo the R/O is permitted but you need specific R/W permissions
- For private repo you need permissions for any read or write actions
- Integrated with IAM
- Near Real-Time Metrics -> CloudWatch
- API actions -> CloudTrail
- Events -> EventBridge
- Replication -> cross-region and cross-account




Next Chapter: [[Chapter 16 - Application Services, Event-Driven & Serverless]]

#AWS_Learning 