NAT is a fundamental technology used in computer networking to allow multiple devices to share a single public IP address. This is essential for businesses and individuals who have more devices than available public IP addresses.

- Designed to overcome the IPv4 shortages
- Translates Private IPv4 Addresses to Public
- Static NAT - 1 private address -> 1 public address (used in AWS Internet Gateway)
- Dynamic NAT - 1 Private Address -> 1st available Public Address
- Port Address Translation (PAT) - many private to 1 public IP with multiple ports (NAT Gateway)

NAT only makes sense for IPv4 and it translates public IPs to private IPs using a NAT table
![[Pasted image 20240208185108.png]]

PAT
![[Pasted image 20240208185325.png]]