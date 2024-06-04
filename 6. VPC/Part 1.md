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
- IPv4 Private CIDR blocks and public IPs (For public resources / communicating with public internet)
- A private private CIDR block is made upon creation. This block is between /16 and /28 subnet sizes.
- A secondary CIDR block can be added if needed, can go upto 5.
- We can get assigned an optional IPv6 address range with /56 subnet mask. All these addresses are publically accessible, but its secure. 
- Come with DNS provided by R53. The DNS is found at base IP + 2. 
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
- Present in every VPC, Highly available.
- Routes traffic b/w subnets in that VPC.
- without a route table and IGW, packets from a subnet can only be sent within the VPC .
- We can create route tables, add subnets of a VPC that conform to these rules and add destination IPs. Then, data from those subnets will be able to go the destination IPs we specified, using the IGW. 
- When default routes are added for IPv4 (0.0.0.0/0) and IPv6(::/0), we can send data to any IP on the public internet when the destination on a packet does not match the address range of the VPC or any other specific destination IPs mentioned in the route table. 
- The destination IP address with highest subnet mask value (more specific) takes priority when multiple routes are matched with a packet's destination address. **NOTE**: Local routes take priority.

### Internet Gateway (IGW)
- Regionally resilient gateway attached to a VPC.
- VPCs can have 0 or 1 gateway, and a gateway can be connected to 0 or 1 VPC.
- Allows services inside VPCs that have public Ipv4 or IPv6 addresses to reach/be reached by the interent and to connect to the AWS public zone.

#### How packets are routed using IGW
The IGW has a map of services that have public IPv4 addresses, and their private Ipv4 addresses. 
The IGW sends and receives packets of data without ever revealing the private IP of a service running in the VPC.

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