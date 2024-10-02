
Virtual machines share the same kernel, but all have different OSs that occupy a big amount of disk and memory. -> The need for virtual machines (which run on the same container engine).

### ECS (Elastic Container Service)

ECS is for containers what ec2 is for docker images -> it manages containers in 1 of 2 modes
1.  EC2 Mode - uses ec2 instances as container hosts
2.  Fargate Mode - serverless, AWS manages the container hosts

ECS runs in a *cluster*, and the images must be in a *registry* (either *DOCKERHUB* or *ECR - Elastic Container Registry*)

To use ECS we need to create a :
- **Container Definition** (which port to use, pointer to where the container is stored, image, etc.) 
- **Task Definition** - defines application as a whole (security, containers ,resources used by the task CPU, mem etc. )
	- in the task definition there is the TASK ROLE (To give containers in the ECS the access they need to communicate with AWS 
	- **A task definition can include one or more containers**
- **Service Definition** - defines an *ECS Service* (instructions on how to balance, scale and restart the EC2s)

