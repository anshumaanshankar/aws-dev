# TOPICS COVERED:
- Sizing and Structure
- Custom VPCs
- Subnets
- Routing, Internet gateway and Bastion hosts

## Sizing and Structure 
Design choices around VPC Design and IP Planning

### Considerations while making a custom VPC - Important
- The size of the VPC: determines how many services can be accomodated.
- Any networks we should _not_ use?: Avoid ranges used by other parties, that we will interact with.
- VPCs can be between /28 and /16 subnet masks (minimum of 16 addresses, max of 65536).
- Plan for the future.

### Case study : Animals for life

- Address ranges we cannot use are:

    USER            | Address Space
    ----------------|----------------
    AWS Pilot       |  10.0.0.0/16
    Ldn Office      |  192.168.15.0/24
    GCP Provider    |  10.128.10.0/9

- Assumptions: 
    - I have 4 AWS accounts
    - I use 5 Regions per account
    - I want to split my VPC into 3 AZs per region + 1 spare
    - I need 3 tiers (web, application, database) + 1 spare in each AZ

### Structuring
**NOTE**: All these splits are suggestions, actual numbers will vary by business size.
Lets assume we want /16 range for each VPC.

1. Let's start at 10.16.0.0, we can go all the way up to 10.127.255.255 without reaching google. This is a /9 space.
2. We know we have 5 regions. Lets allocate 10.16-10.31 for US1, 10.32-10.47 for US2 and so on. Thus, each is a /12 range. We have some leftover space (enough for 3 more regions).
3. In each region, we know we have 4 AWS accounts. This means /14 range for each account.
4. In each account, I can thus have 4 VPCs. This means /16 range for each VPC.
5. Now that we have 4 AZs per region, and 4 tiers in each AZ; we split out VPC into 16 parts. Thus, we get a /20 space for each part (4091 addresses per subnet)

- The complete brakdown of IP address spaces is on [this link](https://learn.cantrill.io/courses/1101194/lectures/26950364). point 5 is not shown in this sheet.

We can work top down or bottom up when we decide on our structure. 

## Custom VPCs
- An isolated network that is disconnected from everything outside it, unless permissions are given.
- Default or dedicated tenancy (has a cost premium).
- IPv4 Private address ranges are assigned to VPCs. 
- public IPs can be assigned to resources inside a VPC in addition to their private IP to enable communicating with public internet.
- A private private address range is made upon creation. The subnet mask is between /16 and /28 for VPCs. 
- A secondary address range can be added if needed, can go upto 5.
- We can get assigned an optional IPv6 address range with /56 subnet mask. All IPv6 addresses are publically accessible (no private IPs). 
- Come with DNS provided by R53.
- Enable DNS resolution and hostnames.

## Subnets
- A subnetwork of a VPC that operates in <ins>one and only one</ins> AZ. 
- A given AZ can have many subnets.
- Subnets in a VPC can communicate w each other.
- The address range of one subnet cannot overlap with another.
- An /56 IPv6 address space can be optionally assigned to a subnet. 
- Auto assign IPv4, DHCP options set, Auto assign IPv6 are subnet level design decisions. 

### Reserved addresses in a subnet
- 5 IP addresses cannot be used in a subnet - The first 4 and the last.
- Eg: For a subnet 10.16.16.0/20 (10.16.16.0 -> 10.16.31.255), The following addresses cannot be used:
    1. Base/starting address: 10.16.16.0
    2. Base +1 address: 10.16.16.1 - Used by the VPC router 
    3. Base +2 address: 10.16.16.2 - Used by DNS
    4. Base +3 address: 10.16.16.3 - reserved for future requirements
    5. Broadcast address: 10.16.31.255 - The last address of the range

## Routing, Internet gateway and Bastion hosts
### VPC Routers
`When data is transmitted from inside a VPC, A VPC router looks at the routing table of the source. If the route to destination is valid, data is sent to the destination.`
- Present in every VPC, Highly available.
- Subnets come with a routing table that automatically allows transmission of data within the VPC.
- We can make new routes with public destination IPs and associate these routes with a subnet to enable public transmission, using IGW.
- If we want to enable public transmission, the resource will need to have a public IP in addition to its private IP inside the VPC. 
- Default routes for IPv4 (0.0.0.0/0) and IPv6(::/0), allow data to any public destination IP. If no specific route is matched with destination, this default is applied.
- When multiple destination IPs are matched with a rule, The destination address with highest subnet mask value (more specific) takes priority. **NOTE**: Local routes take priority.

### Internet Gateway (IGW)
- Allows resources inside the VPC to communicate with the public internet. These resources will have a public IPv4 address.
- The IGW has a map of resources that have public IPv4 addresses along with their private Ipv4 addresses. 
- The IGW masks the private IP of the resource, and changes the address from its public to private or vice versa for incoming and outgoing transmissions resp.
- VPCs can have 0 or 1 gateway (region resilience) and a gateway can be connected to 0 or 1 VPC.

1. Sending from VPC
    - EC2 instance has a packet with source (its private Ipv4) and destination IP.
    - On transmission, route table directs packet to IGW.
    - IGW sees where packet is from, and changes the source address to that EC2 instance's public IPv4 address from private.
    - IGW forwards packet to destination, which only sees that public IP.
    ![alt text](<Screenshots/Screenshot 2024-06-03 at 2.57.13 PM.png>)

2. Sending from server
    - Server sends packet to the instance's public IP, with its IP as source
    - This packet arrives at the IGW via the public internet
    - IGW changes destination address from the public IP to the instance's private IP
    - Forwards the packet to the EC2 instance through the VPC's network.
    ![alt text](<Screenshots/Screenshot 2024-06-03 at 3.00.39 PM.png>)

All Ipv6 addresses are publically routable. 

### Bastion host / Jumpbox
- Used as entry point for private only VPCs. 
- Instances inside a public subnet where all incoming management connections arrive and then access internal VPC resources.


## DEMO - how to enable subnets to send data onto public internet
1. Create the subnets on your VPC, create a public IPv4 address for them.
2. Make an IGW, connect it to the VPC.
3. Create a route table, add subnet associations and destination IPs.
