### What it does?

The Domain Name System (DNS) is a core part of most applications and IT systems. DNS uses a fairly complex hierarchical structure to allow scaling and delegation from domain names to IP addresses.

### Why do we need lots of servers?

- to avoid risk
- all people use DNS - scaling
- huge amount of data to be stored ( ~341M domains that have more than one record )

*e.g. Facebook has fb.com, facebook.com, etc. which all point to the same IP*

![[Pasted image 20240208231055.png]]

### Hierarchical Design

![[Pasted image 20240208231444.png]]

### Flow

![[Pasted image 20240208231656.png]]

**Alternative description:**

![[Pasted image 20240208231753.png]]


### Domain registering

**Components:**
- Domain Registrar ( lets you purchase and register the domain ) -- **in AWS is Route53**
- DNS hosting provider ( they operate DNS Name Servers -> host DNS zones) -- **in AWS is Hosted Zones** 

**Steps:**
- Buy domain on the Domain Registrar
- If the company has a zone, get it. Else, DNS Hosting Provider creates it
- The Domain Registrar supplies the TLD with the NS info
- The .com TLD adds the domain to the TLD .com zone
- The DNS Hosting provider gets the data to the .com zone

![[Pasted image 20240208232635.png]]

#networking 