## TOPICS COVERED
- Public vs Private Services
- Global Infrastructure
- VPC Basics
- EC2
- S3


## Public v Private Services
**TLDR**: There are 3 different network zones: Public internet, AWS public zones, AWS private zones. 
    - AWS public zones host public services that can be accessed from anywhere with an internet connection and permissions (eg: S3). AWS public zones are connected to the internet, not a part of it.
    - AWS private zone hosts VPCs which hosts private services. These services can only be accessed from inside the VPC and by connecting to it. 

![alt text](<Screenshots/Screenshot 2024-05-17 at 1.59.00 PM.png>)

## AWS Global Infrastructure
- Regions: AWS infrastructure in different parts in the world, connected using high speed networking links 
- Edge locations: local distribution points that are smaller than regions. Useful for applications that need low latency. 

- Benefits of regions:
    - Independence from one another, totally isolated
    - Geopolitical / governance separation. A region in a country is bound by the laws of that region
    - Give you location control.

- Availability zones (AZ): Inside every region are multiple AZs which are independent of each other. For a region ap-southeast-2, the AZs will be called ap-southeast-2a, ap-southeast-2b and so on. if one AZ is impacted, the services held in other zones remain available for usage. We can place the same service in multiple AZs to provide resilience (region resilient). 

- Resilience:
    - Global resilience: one database for a service that is replicated in multiple regions. Its a global service (eg. IAM). Failure occurs when all regions fail.
    - Regional resilience: A service operating in one region. Data is replicated to many AZs in that region. We could have the same service in many regions, but they would be seperate from each other. Failure occurs when 1 region fails completely. NOTE: A service may be offered on more than one region, but they are independent of each other. (Eg. EC2 in N. Virginia is not the same as EC2 in Sydney)
    - AZ resilience: services that are run from a single AZ. Failure occurs when the AZ fails.

## VPC (Tres important)
- A VPC is made in one region for an AWS account. They are regionally resilient. 
- **In a region, we can have 1 default VPC that comes preconfigured. We can have many custom VPCs, which we build end-end.**
- Every VPC is allocated a range of IP addresses (VPC CIDR - Classless Inter Domain Routing). Any communication with the VPC happens via the CIDR. Outgoing connections originate from the CIDR.
- Custom VPCs can have many CIDRs, but the default has only one and its always 172.31.0.0/16. Each subnet of a VPC is in one AZ of the region and each use a part of the CIDR range. Image below shows a region with 3 AZs, each having a range of IPs from the CIDR range. 

![alt text](<Screenshots/Screenshot 2024-05-17 at 2.47.06 PM.png>)

- **Summary of Default VPCs** :
    - One per region, can be removed and recreated
    - Default VPC CIDR is <ins>172.31.0.0/16</ins>
    - Each AZ gets a /20 subnet of the VPC
    - It comes with an internet gateway, security group and NACL
    - anything deployed in a subnet has a public IPv4 address


## EC2
- its a private AWS service that provides access to virtual machines called instance which is launched inside a subnet. 
- An instance can be of different size and capabilities (GPUs, storage and networking etc)
- an instance is billed by the second - charge for memory, storage and any extra
- states of an instance:
    - Running: Charged for memory, CPU, network, disc
    - Stopped: Charged for disc because storage is still allocated for the instance
    - Terminated: No charge. When an instance is terminated, all allocated storage is deleted
- Connecting to EC2: Remote desktop protocol (port 3389) is used to connect to EC2 instances on windows. You can SSH (port 22) into an EC2 instance from linux.

    
## S3
- public service (with permissions) that serves as a global storage platform
- delivers two main things:
    - Objects: Basically, files. Made up of two main components:
        - Key: file name
        - value: the contents of the object / the data. The size can be from 0b to 5TB
        - other components include version ID, metadata, access control and subresources
    - Buckets: 
        - Container for objects that are created in specific regions. This means that the data inside a bucket has a primary home (that region). 
        - IMPORTANT: Bucket names must be globally unique
        - can hold unlimited number of objects (unlimited bytes of data)
        - Bucket versioning prevents overwriting of objects that have the same name
        - there is no hierarchy, all objects are listed at the same
        - when we make objects with / (eg: /old/file1.jpg) at the beginning, S3 represents a folder but there actually is not there - folders are called object prefixes.
        - 100 buckets per aws account (soft limit) upto 1000 buckets (hard limit)
        - Its an object storage system, not files or blocks (can't be mounted as a mount point).

## CloudFormation
- Lets you create, update and delete infrastructure using <ins>templates</ins> on AWS.
- Templates can be reused to make/delete/updates resources much faster than when done manually.
- Templates are written in YAML or JSON.

- **Components of a template**:
    - Resources: that is what tells cloudformation what to do (Create/Update/Delete). This is the only mandatory part of the template.
    - Description: allows the author add a description about what the template does. If we have a <ins>template format version</ins> (another field), the description must follow it.
    - Metadata: controls the UI, among others.
    - Parameters: Fields that prompt the user for more info, we can add criteria and default values of those parameters too.
    - Mapping: allows lookup tables to be made. 
    - Conditions: Allows decision making in the template - certain things only occur when conditions are met. 
    - Outputs: once the template is made, we see outputs to show what is created / updated or deleted. For example, an instance ID of an EC2 instance that is made.

- The example template used for the demo is in the same folder labeled ec2instance.yaml
