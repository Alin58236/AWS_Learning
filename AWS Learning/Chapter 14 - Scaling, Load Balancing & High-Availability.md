

![[Screenshot 2025-07-10 at 13.53.22.png]]

### ELB Architecture

- when you provision a load balancer in a Region, the Load Balancer places 1+ ***Load Balancer Nodes*** into a subnet in each AZ and scales with load

- when it is created, its created with one DNS record ( and A Record ) -> this resolves to the ELB Nodes

- another choice that we need to make when creating a load balancer is if it is internet-facing (public IPs) or internal (private IPs)

- Load Balancer Nodes are configured with listeners which accept traffic on a protocol and port and communicate with targets on certain protocols and ports

- a ***PUBLIC ELB***  CAN CONNECT TO PRIVATE EC2 INSTANCES!!!! The INSTANCES DONT HAVE TO BE PUBLIC ALSO

- Load Balancers need to have 8+ ips in the subnet and a /27 or larger subnet to allow for scale


### Cross-Zone LB

 - The ability to distribute LB in multiple AZs
 
 - Used for uneven distribution of load across multiple AZs

![[Screenshot 2025-07-10 at 14.16.29.png]]



### Application Load Balancing vs Network Load Balancing

#### Application Load Balancer

- Layer 7 Load Balancer (Http / HttpS)
- But it doesn't understand any other Layer 7 protocol (SMTP,SSH,Gaming stuff)
- No TCP/UDP/TLS Listeners
- Supports Content-Type, Cookies, custom headers, user location and app behaviour
- You can't have an unbroken SSL connection between client and application layer - it is always terminated at the ALB
- a new connection is made up to the termination
- All application load balancers that use https need to have valid SSL Certificates
- ALBs are slower than NLBs, but they can evaluate application health

- we have rules that direct connection which arrive at a listener
- processed in priority order
- Default = catch all
- ***Rule Conditions : host-header, http-header, http-request-method, path-pattern, query-string & source Ip***

- ***Actions: forward, redirect, fixed-response, auth-oidc, auth-cognito***


#### Network Load Balancer

- Layer 4 Load Balancer -> tcp, udp, tls, tcp_udp
- no visibility or understanding of HTTP or HTTPS
- no headers, no cookies, no session stickiness

- ***Really Really Really fast*** (Millions of requests per second)

- Health checks just check ***TCP handshake -> not app aware***

- ***NLBs can have static IP's*** - useful for whitelisting
- Forward TCP to instances -> ***unbroken encryption***



Unbroken encryption -> NLB
Static IP for whitelisting -> NLB
The fastest -> NLB
Protocols not HTTP or HTTPS -> NLB
PRIVATELINK -> NLB

Else 

ALB ( Application Load Balancers )



### Launch Configuration and Templates

- they allow you to define the config of an EC2 instance in advance
- AMI, Instance Type, Storage, Key-Pair
- Networking and Security Groups
- Userdata & IAM Role
- Both are NOT editable - defined once, LT has versions
- LT provides newer features - including T2/T3 Unlimited, Placement Groups, Elastic Graphics


***Launch Configurations*** are used as part of ***Auto Scaling Groups***

***Launch Templates*** are used by ASGs but they can also launch EC2 instances from the CLI




### Auto Scaling Groups

- they provide autoscaling for EC2 (self healing architecture)
- uses launch templates or configurations (what to scale)
- Has a minimum size, desired and max size (1 - min :2 - desired :4 - max)
- Keep running instances at the desired capacity by **provisioning** or **terminating** instances

##### Scaling Policies

- basically rules for scaling

1. **Manual Scaling**- manually adjust the desired capacity
2. **Scheduled Scaling** - Time based adjustment  - e.g. Sales
3. **Dynamic Scaling** 
	1. Simple = "CPU Above 50% +1", "CPU Below 50% -1"
	2. Stepped = Bigger +/- based on difference
	3. Target Tracking = Desired aggregate CPU at 40% -> ASG Handle it

- **Cooldown period** = how long to wait after a scaling action before performing another


ASG are cool on their own but much cooler when integrated with a Load Balancer.

The ASG can put instances in Target Groups of Load Balancers


![[Screenshot 2025-07-10 at 17.02.37.png]]


***!!!! AUTO SCALING GROUPS ARE FREE !!!!***

- Only the resources created are paid
- Use cool downs to avoid rapid scaling
- more smaller instances are better - granularity
- use with ALB for elasticity
- ***ASG*** defines ***when and where***, ***LT*** defines ***WHAT***



### ASG Lifecycle Hooks

- ***Custom Actions*** on instances during **ASG Actions**
- Instance Launch or instance terminate transitions
- Instances are paused within the flow .. they wait
- or you resume the ASG Process CompleteLifecycleAction
- EventBridge or SNS Notifications

useful to add custom actions before or after provisioning / terminating

![[Screenshot 2025-07-10 at 17.10.15.png]]


### Health Checks

ASG asses the instances using health checks


- EC2 (default), ELB (can be enabled) & custom
- EC2 - Stopping, Stopped, Terminated, Shutting down or Impaired (not 2/2 status) = Unhealthy
- ELB - Healthy = Running & passing ELB health check
- can be more application aware (Layer 7)
- Health Check grace period (default 300s - 5min) - Delay before starting checks -> allows system launch, bootstrapping, and application start


Next Chapter: [[Chapter 15 - AWS Code*]]

#AWS_Learning 