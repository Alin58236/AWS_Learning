
### R53 Public Hosted Zones

Route53 has 2 types of hosted zones : 
- public 
- private

A Route53 hosted zone is a DNS database for a domain (eg. vms.com). It is globally resilient (multiple dns servers). It can either be created with a domain acquisition via R53, or created separately.

A public hosted zone is a DNS database - so it is a zone file accessible from the public internet and VPCs.
Architecturally, when we create a r53 hosted zone our domain is listed on 4 *R53 Name Servers*. We then must use **NS Records** to point to the four *R53 Name Servers* (connect to the global DNS. In these *Hosted Zones*, there are created **Route Records,** which are the actual records that the DNS uses.

**Externally registered Domains can point to a R53 Public Zone**

![[Screenshot 2025-03-30 at 17.34.27.png]]

