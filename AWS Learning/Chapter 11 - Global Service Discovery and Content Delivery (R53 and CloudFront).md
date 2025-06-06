
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


## CloudFront

### Architecture

CloudFront is a CDN (Content Delivery Network)

It delivers content from its original location to the viewers of that data by caching it in a global network

- **Origin** - the source of your content (S3 origin or custom origin)

- **Distribution** - the 'configuration' unit of CloudFront

- **Edge Locations** - Local Caching locations throughout the network

- **Regional Edge Cache** - Aditional *Bigger* Edge Locations for another layer of caching


**Easily integrated with ACM (Amazon Certificate Manager) for SSL Cerificates**

It only performs Read Caching ( for downloads ), no write caching - writing is done only at the Origin

![[Screenshot 2025-06-03 at 13.26.50.png]]


The 'distributions' are important for CloudFront, but they are the place where the most important configurations are located. Those are **'Behaviours'** which are contained **WITHIN** 'distributions'. (it's like a sub-configuration)

Every distribution has a default behaviour, but we can define custom ones that are of a higher priority..

*e.g.*

Let's assume we have 2 buckets, one with low-security content (default behaviour which matches the pattern \*) and one with restricted content which matches 'img / \*'

![[Screenshot 2025-06-03 at 13.34.24.png]]



### CloudFront Behaviours


-  cheaper version deploy only to NA and EU edge locations

- We can pick the TLS security policy ( if the latest one is picked, it is the most secure but older browsers may not be able to access it )

- It has configurable logging stored in an S3 Bucket

- Behaviours are created using match patterns

- We can restrict viewer access to a behaviour - then we need a trusted key view or a trusted signer
 

### TTL and Invalidations

Let's assume we have a simple architecture of a CloudFront Distribution:

1. 1 Origin
2. 1 edge location
3. 3 customers

An image called IMG1 is uploaded to the Origin but it is not yet in the edge location. When Customer1 requests that image, it is put in the Edge location and retrieved by the customer.

Now the image IMG1 is obsolete, and it is replaced by IMG2 in the Origin, but when **Customer2** requests the image from the Origin, it will receive IMG1 (the old one) because it was already in the edge location, so CloudFront didn't think it is necessary to fetch the content from Origin.

Eventually the image in the Cache (Edge Location) expires, but it is not deleted, but becomes **STALE**

When Customer3 makes a request, 2 things can happen:

1. If the content in the Origin is the same version as the Stale one in the Edge Location, it is made visible again for {{TTL}} time. 
		-> ***304 Not Modified*** is returned.
		
2. If the content in the Origin is newer than the one in the Edge Location, the content will be replaced and made visible for {{TTL}} time.
		-> **200 OK** is returned

![[Screenshot 2025-06-03 at 14.47.40.png]]

### Invalidations 

- are applied at a distribution level
- on all edge locations
- wildcard match pattern
- if we need to invalidate often, it is better to just use versioned filenames instead of invalidations

### SSL/TLS

#### Cloudfront And SSL

- CloudFront Default Domain Name (CNAME) - https://{{Random-Shit}}.cloudfront.net/

- SSL Supported by default \*.cloudfront.net Certificate

- Most of the time you want to use Alternate Custom CNAMES e.g. cdn.whatever...

- Verify Ownership (optionally HTTPS) using a matching certificate

- Generate or import a certificate using the ACM (regional service!!!) --- GLOBAL CERVICES NEED CERTIFICATES CREATED ALWAYS IN US-EAST-1 !!!!!!!!!!!

We usually need 2 ssl connections:
1. Between the viewer and the edge location(CloudFront) - Viewer protocol
2. Between the CloudFront and the Origin - Origin protocol

Both need valid certificates and intermediate certificates


#### SNI

![[Screenshot 2025-06-03 at 17.10.02.png]]


!!! YOU CAN NOT USE SELF-SIGNED CERTIFICATES, ONLY PUBLICLY TRUSTED ONES !!!!


![[Screenshot 2025-06-03 at 17.15.09.png]]


### Origin Types and Architecture

#### Origin Types:

1. S3
2. AWS Media Package Channel Endpoints
3. AWS Media Store
4. Everything else - WEB SERVERS / CUSTOM ORIGINS

To restrict Access to S3 origins we can use an Origin Access Identity (OAI) or an Origin Access Control (OAC)

Also, if we want to pass through some headers, these can be configured as well.

If we are not using S3 origins then we don't have the posibility to use OAI and OAC


### AWS Certificate Manager (ACM)

- ACM can run either as a public or a private Certificate Authority

 - The certificates can be either ***imported***, or ***generated***

	-  If the certificate is ***imported***, renewal must be done manually by the admin
	- If the certificate is ***generated***, ACM manages the renewal for you


- Certificates can be deployed out to ***supported services***

- Supported AWS Services ***ONLY (CLOUDFRONT AND ALBs.... NOT EC2***)


The ACM is a regional resilient service.


- FOR MOST SERVICES, MOST OF THE CERTIFICATES NEED TO BE IN THE SAME REGION AS THE SERVICE THAT IT IS DEPLOYED IN..... 

- FOR CLOUDFRONT IT NEEDS TO BE ALWAYS IN THE ***US-EAST-1***



### CF Security - OAI & Custom Origins

- Custom Origin = Any non-s3 origin

- OAI is a type of identity

- It can be associated to a CloudFront distribution

- CloudFront ' becomes ' that OAI

- that OAI can be used in ***S3 Bucket Policies***

- Deny ALL but ONE OR MORE OAIs


![[Screenshot 2025-06-05 at 16.20.37.png]]

!!! We create an OAI and give it an explicit ALLOW in the Bucket Policy


#### Securing Custom Origins

If we have custom (non s3) origins, we want them to be secured, as a customer must not directly access them. To do this, we configure CloudFront to add a custom header, and the origin requires this custom header. If the header doesn't appear, the origin refuses connection.

Another way to restrict access is to create a firewall around an origin and give it an IP_RANGE of the edge locations.



### Private Distributions

#### Signed URLs and Cookies

- CF can run in 2 security modes:
	1. public
	2. private - requests need signed cookie or URL

- We can have only one behaviours - either the distribution is ***public*** or ***private***
- Usually we should use multiple behaviours

There are 2 ways of creating private distributions:

1. New way - using a signer (if a behaviour has a signer attached, that can sign cookies, that behaviour is private)
2. Old way - A CF key is created by an account root user -> the account is added as a trusted signer


###### Signed URLs provide access to ONE OBJECT - useful if the client doesn't support cookies

###### Signed Cookies provide access to groups of files / all files of a type


![[Screenshot 2025-06-06 at 22.06.49.png]]

### CloudFront Geo-Restrictions

- CF Geo Restrictions

	- Whitelist / Blacklist format
	- Works with Countries Only
	- 99.8+ % accuracy
	- APPLIES TO THE ENTIRE DISTRIBUTION
	- ![[Screenshot 2025-06-06 at 22.13.20.png]]

- 3rd Party Geo Location
	- Completely Customizable
	- ![[Screenshot 2025-06-06 at 22.15.57.png]]


### Field-Level Encryption

![[Screenshot 2025-06-06 at 22.21.01.png]]

![[Screenshot 2025-06-06 at 22.25.30.png]]



### Lambda @ Edge

- You can run lightweight Lambda at edge locations
- Adjust the data between the Viewer and Origin
- Currently supports node.js and python
- They run in the AWS Public Space ( not private )
- Layers are not Supported
- Different Limits to the 
![[Screenshot 2025-06-06 at 22.34.34.png]]


##### Use Cases

![[Screenshot 2025-06-06 at 22.38.04.png]]



Next Chapter: [[Chapter 12 - SQL]]

#AWS_Learning 

