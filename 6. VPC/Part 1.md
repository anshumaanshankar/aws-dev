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
On premise(AUS) |  192.168.10.0/24
AWS Pilot       |  10.0.0.0/16
Azue Pilot      |  172.31.0.0/16
Ldn Office      |  192.168.15.0/24
NYC office      |  192.168.20.0/24
Seattle Office  |  192.168.25.0/24
GCP Provider    |  10.128.10.0/9

- Assume we have 4 AWS accounts. In each, we have 3 US regions, 1 in Europe and 1 in Aus => 5 regions per account
- Therefore, 5 regions * 2 ranges for each = 10 ranges are needed per account. 
- Thus, we need 40 ranges. 

- Assumptions: 
    - I have 4 AWS accounts
    - I use 5 Regions per account
    - I want to split my VPC into 3 AZs per region + 1 spare
    - I need 3 tiers (web, application, database) + 1 spare in each AZ

### Structuring
**NOTE**: All these splits are suggestions, actual numbers will vary by business size.
- Lets assume we want /16 range for each VPC.

1. Let's start at 10.16.0.0, we can go all the way up to 10.127.255.255 without reaching google. This is a /9 space.
2. We know we have 5 regions. Lets allocate 10.16-10.31 for US1, 10.32-10.47 for US2 and so on. Thus, each is a /12 range. We have some leftover space (enough for 3 more regions).
3. In each region, we know we have 4 AWS accounts. This means /14 range for each account.
4. In each account, I can thus have 4 VPCs. This means /16 range for each VPC.
5. Now that we have 4 AZs per region, and 4 tiers in each AZ; we split out VPC into 16 parts. Thus, we get a /20 space for each part (4091 addresses per subnet)

- The complete brakdown of IP address spaces is on [this link](https://learn.cantrill.io/courses/1101194/lectures/26950364). point 5 is not shown in this sheet.

We can work top down or bottom up when we decide on our structure. 