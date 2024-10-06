
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


### Cluster Modes
#### 1. EC2 Mode
- out-of-the-box management high level components
- the ECS cluster is created within a VPC on our AWS Account -> it benefits from multiple AZs
- When the EC2 instances are provisioned, we WILL BE PAYING for them, regardless of the containers running on them
#### 2. ECS - Fargate Mode
- Serverless mode
- out-of-the-box management, **no need to manage the ec2 instances yourself**
- **no need to pay for the hosts**
- the ec2 cluster resources are allocated from a shared pool, but we have no visibility of other customers
- Different from the EC2 mode, the tasks are injected into your vpc, and each is given an **elastic network interface**


### When to use the modes?

1. ECS (EC2 Mode)
	-  if you use containers
	- Large workload, price consciousness

2. EC2 
	-  quickly test containers using ec2 as a docker host

3. ECS (Fargate)
	- Large workload, overhead consciousness
	- small / burst loads
	- batch loads

### ECR ( Elastic Container Registry) - DockerHub but for AWS
	- main advantage = integrated with IAM



### Kubernetes 101

##### Cluster structure:
1. ***Cluster control plane***:
	1. it contains multiple compute resources that work as one unit
	2. it controls the applications, scaling and deploying
	3. Contains **NODES** which can be VMs or servers inside the cluster
	4. Contains the **kube-apiserver** (the front-end of the cluster, can be horizontally scaled)
	5. Contains the **etcd** - key-value store
	6. Contains the **kube-scheduler** assigns the resources to the pods
	7. Contains the **cloud-control-manager** which is a cloud specific control component
	8. Contains the **Kube-controller-manager** which contains the following processes:
		1. Node Controller 
		2. Job Controller
		3. Endpoint Controller
		4. Service Accounts & Token Controllers

2. ***Nodes***
	1. these contain one of 2 software runtimes:
		1. **containerd** 
		2. **Docker**
	2. Also contain *kubelets* : agents that talk to the Cluster Control Plane
	3. Contain **PODS** that contain the actual applications (but usually is one pod / container)
	4. Contains a **k-proxy**

3. **Ingress**
	1. A way to get into a service, hosted on a pod, from the outside of the cluster 



### Elastic Kubernetes Service (EKS) 101

- EKS is a Kubernetes Service Managed by AWS - open source and cloud agnostic
- The Cluster Control Plane scales and runs in multiple AZs 
- Integrates with VPCs, and other AWS Services
- EKS Cluster = EKS Control Plane + EKS Nodes
- **etcd** is distributed across multiple AZs
- Nodes can be:
	- self-managed
	- managed node groups
	- Fargate nodes

Architecture:
![[Screenshot 2024-10-06 at 15.53.20.png]]


# Next Chapter : [[Chapter 9 - Advanced EC2]]

#AWS_Learning 