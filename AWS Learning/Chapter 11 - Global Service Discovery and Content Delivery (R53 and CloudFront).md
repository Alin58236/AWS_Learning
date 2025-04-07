
### R53 Public Hosted Zones

Route53 has 2 types of hosted zones : 
- public 
- private

A Route53 hosted zone is a DNS database for a domain (eg. vms.com). It is globally resilient (multiple dns servers). It can either be created with a domain acquisition via R53, or created separately.

A public hosted zone is a DNS database - so it is a zone file accessible from the public internet and VPCs.
Architecturally, when we create a r53 hosted zone our domain is listed on 4 *R53 Name Servers*. We then must use **NS Records** to point to the four *R53 Name Servers* (connect to the global DNS. In these *Hosted Zones*, there are created **Route Records,** which are the actual records that the DNS uses.

**Externally registered Domains can point to a R53 Public Zone**

![[Screenshot 2025-03-30 at 17.34.27.png]]

### R53 Private Hosted Zones

- Like a public zone but not public :)))
- Associated with VPCs
- It is only accessible in those VPCs
- Split-view (overlapping public and private)

![[Screenshot 2025-04-01 at 14.46.55.png]]

#### Split Horizon DNS

![[Screenshot 2025-04-01 at 14.48.44.png]]

Used for using the same domain access for public and internal business needs.


### CNAME vs R53 Alias

##### In DNS, an "A" record maps a **NAME** to an **IP ADDRESS**
(eg.) instagram.com -> 14.42.24.51

A ***CNAME*** maps a ***NAME*** to another **NAME**
(eg,) www.instagram.com -> instagram.com

*With just a CNAME, Elastic Load Balancer would be invalid*

##### An **ALIAS** record maps a **NAME** to an **AWS Resource**

- Can be used for both **naked/apex** and **normal** records
- There is no charge for **ALIAS** requests pointing at **AWS Resources**
- For AWS SERVICES - default to picking ALIAS


### R53 Simple Routing

![[Screenshot 2025-04-01 at 16.05.13.png]]

### R53 Health Checks

- Health Checks ***are separate*** from, but are ***used by*** records
- Health checkers are located globally
- We can have TCP, HTTP/HTTPS or HTTP/HTTPS with String Matching Checks
- Endpoint Tests / Cloudwatch Alarms / Checks on Checks :))

We also have a way to route traffic to a resource when the resource is healthy, or to another one when the resource is unhealthy
##### !!! WATCH using R53 and Failover Routing DEMO Part 1 for homepage and failover deployment + setup !!!


### Multi Value Routing

- Another routing policy in R53
- A combination of simple and failover routing
- Can create many records all with the same name
- When we define multiple values, (all health checked) 8 healthy ones are returned
- IT IS NOT A SUBSTITUTE FOR A LOAD BALANCER!!!

### Weighted Routing

Let's assume that we have 3 records in the hosted zone. 

Each record has a weight (40,40,20) -> the total weight sums 100 (much like precentages)

- This means that the IPs will be resolved based on their weight
- Setting a weight to 0 will make the IP unresolvable.
- If a chosen record is unhealthy, the process reruns until a healthy record is returned

** seen as simple load balancing ** 



### Latency Routing

- Latency Based Routing should be used when we are trying to optimize for performance and user experience.
- it supports ***one record with the same name in each region***
- AWS maintains a database of latency between the users general location and the regions tagged in the records (The DB is not realtime tho)
- The lowest latency healthy record is returned


### Geolocation Routing

It is similar to ***Latency Routing*** but instead of the latency, the users are tagged with their location (country codes / default / or continent code)

It checks from the state -> country -> continent -> default

Can also be used for regional restrictions

This doesn't return the closest location, but the most relevant one!!!


### Geoproximity Routing

It aims to provide records that are ***as close to the customer as possible***

Records can be tagged with an AWS Region or *lat* & *long* coordinates

We can define a ***+/- BIAS*** for geoproximity calculations (eg. make ***Saudi Arabia*** Customer access ***Australia*** resource instead of ***UK*** resource)


### Interoperability

We can use R53 to register domains!

R53 can do Domain Registration, Hosting, Or BOTH


