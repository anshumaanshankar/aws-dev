# TOPICS COVERED
- Virtualization 101
- Architecture and Resilience
- Instance Types
- Storage Refresher
- ELastic Block Store (EBS) Service Architecture
- EBS Volume Types

## Virtualization 101
**Definition**: Running more than one operating system on a piece of hardware. Allows multiple "previleged applications" to run on the same hardware. That is, diff apps on diff operating systems. 

Types of Virtualizations:
1. Emulated Virtualizations (Or software virtualization): Hosting multiple "guest" operating systems on virtual machines, that believed they ran on real hardware, thus their apps were previleged. These are connected to the actual hardware using the host operating system / hypervisor. These operating systems are using memory and disk on local hardware, thinking its their own. The hypervisor performs "binary translation" - used to prevent one guest OS's content being overwritten by another etc. <ins>Very slow for demanding workloads</ins>

2. Para virtualization: Only some Operating systems can be used on virtual machines for this. In this method, areas of the guest OS that usually make calls to the hardware, now make "hypercalls" to the hypervisor in use. By doing this, we improve performance. 

Both 1 and 2 have a set up that looks like this.
![alt text](<Screenshots/Screenshot 2024-06-04 at 1.38.30 PM.png>)

3. Hardware assisted virtualization - The hardware itself is aware of the virtualization. The CPU expects instructions from the guest OS, which is redirected to the hypervisor which handles the execution. This is even faster. **Drawback**: The VM still think they have actual hardware, when they don't - they actually use cards on the hypervisor. This means, when we perform high transactional activities, performance is impacted. Lot of CPU cycles are consumed on the host.

![alt text](<Screenshots/Screenshot 2024-06-04 at 1.48.08 PM.png>)

4. SR-IOV (Single Route IO Virtualization) - Allows add-on cards to present itself as several mini-cards that are unique. These are presented to the OS, dedicated for its use. Thus, the guest OS can use these cards directly without needing the hypervisor. <ins>Used for advanced networking now.</ins> 
![alt text](<Screenshots/Screenshot 2024-06-04 at 1.48.40 PM.png>)

## Architecture and Resilience
- EC2 instances are Virtual machines (OS + resources) that run on EC2 hosts (AWS managed hardware). Hosts can be shared or dedicated.  
- EC2 hosts run in a single AZ of a region. They have local temp storage called instance store & 2 types of networking (storage and data).
- A primary elastic network interface (ENI) is provisioned in a subnet and mapped to the physical hardware of the EC2 host. Multiple network interfaces can be mapped to a single host, even in different subnets. Same AZ is needed though. 
- Instances of the same type and size mostly share the same host. 
- Hosts can connect to the Elastic BLock Store (EBS), which is AZ resilient and used for remote storage.    
- Instances stay on a host till
    - A host fails or is taken down
    - If an instance is stopped and then started (not the same as restart)

### EC2 use cases
- Traditional OS + application compute needs
- Persistent, Long running compute needs
- Server style applications
- steady-state or burst load
- Monolithic application stacks
- Disaster recovery

## Instance Types
The instance type determines:
1. The raw amount of resources you get (CPU, Memory, Local storage) and their ratio.
2. Storage and Data network bandwidth - performance varies
3. System architecture
4. Additional features and capabilities

### Instance Categories:
![alt text](<Screenshots/Screenshot 2024-06-04 at 2.32.40 PM.png>)

### EC2 Instance Types
![alt text](<Screenshots/Screenshot 2024-06-04 at 2.39.43 PM.png>)

### Breaking down an instance name
1. Letter: instance family (T, M, I, R et al.).
2. Number: Instance Generation (eg. R5 means fifth generation of R, where R is the instance family).
3. Additional capabilities (not required): defines the additional capabilities. 
4. Instance size: Within a family and generation, there's various sizes that determine how much memory and CPU are allocated (nano, micro et al).

## Storage refresher
### Types of Storage
- Direct attached / local attached storage: directly connected to EC2 (instant store)
- Network attached: volumes delivered over the network. For EC2, this is done via EBS.
- Ephemeral storage: temporary storage that cannot be relied on for persistence. Eg: Direct attached storage in AWS.
- Persistent storage: Lives beyond the lifetime of the device its attached to. Eg: Network attached storage in EBS. 

### Storage categories
- Block Storage: volumes presented to the OS as a collection of blocks. It has no inbuilt structure. <ins>Its mountable, bootable</ins>. The OS has to build a file system on top of this.
- File Storage: Presented as a file share, has a structure. <ins>Mountable, not bootable.</ins>
- Object Storage: Stores a flat collection of objects with no hierarchy. Objects can be any type of data. Benefits are scalability, lots of simultaneous users possible. <ins>Not mountable, not bootable.</ins>

### Storage performance metrics
- IO/block size: The size of the block of data being written to disc.
- IOPS: Input Output operations per second that can be supported by the storage system.
- Throughput: rate at which data is transferred between the storage system (EBS volume) and the host system (EC2 instance)
All three metrics are related. 

`Possible throughput = block size * IOPS.`

**Systems might have throughput caps, or as the block size is increased, IOPS may decrease.**

## EBS Service architecture
- Provides block storage which are raw disc allocations.
- EBS volumes are attached to one EC2 instance at a time. They can be detached from one instance and attached to another - they're independent of the instance.
- Instances see a block device on top of which they make file systems. 
- Storage is provisioned in one AZ. 
- When a "snapshot" is taken of a volume, its stored in a portion of S3 managed by EBS. Using this snapshot, we can create the volume in a different AZ. 
- Billed at gb-month (1gb per month = 2gbs per half month).

![alt text](<Screenshots/Screenshot 2024-06-04 at 6.33.14 PM.png>)
<p> We have 2 AZs in us-east-1, 1 AZ in ap-southeast-2. The EBS in each AZ are independent. Snapshotting volumes allow usage in different AZs, regions. EC2 instances cannot connect to EBS volumes in different AZs or regions. 

## EBS Volume Types
### General Purpose SSD - GP2
- A GP 2 volume can be b/w 1gb and 16tb in size.
- Max. throughput of GP2 is 250mbps. 
- Each volume comes with an 'IO Credit Bucket' with 5.4 million initial IO credits. Each IO credit is 16kb, meaning 16kb can be handled per second by one credit. On top of this, the bucket refills at the rate of Baseline Performance.
- If the volume size is <= 33.3gb, we get 100 credits/second (IOPS). For volume > 33.3gb, we get 3*size IO credits per second. That is, for a volume of size 70gb, we would get 70*3 = 210 IO credits/second aka 210 IOPS.  
- We can "burst" upto 3000 IOPS, emptying our credit bucket. That is, we can perform up to 3000 Input/Output operations in a second. 
- **NOTE**: The number of credits inputted per second is capped at 16,000. That means, for all volumes of size 5.33tb and larger, there is no increase in IOPS over 16,000. At 1tb, the number of credits entering per second exceeds the burst of 3000 IOPS.

### General Purpose SSD - GP3
- `20% cheaper than gp2, mostly even when paying extra`.
- Size of the volume is b/w 1gb and 16tb, same as GP2.
- No credit architecture system like in GP2. 
- Each volume, regardless of size, performs 3000 IOPS and throughput of 125 mbps.
- Both IOPS and throughput can be increased by paying extra - upto 16,000 IOPS and 1000mbps.

### Provisioned IOPS SSD - io1 io2, io blockexpress

Metric          | io1           | io2           | Block express
----------------|---------------|---------------|---------------
Max throughput  |1000mbps       |1000mbps       | 4000mbps
Max IOPS        |64,000         |64,000         | 256,000
Size            |4gb-16tb       |4gb-16tb       | 4gb-64tb
IOPS by size    |50 IOPS per gb |500 IOPS per gb| 1000 IOPS per gb

Per instance performance: The maximum performance that can be acheived between EBS service and an EC2 instance. Its influenced by type of volume, type of instance and size of the instance. 

Assuming ideal type and size of instance; Max per instance performance of:
1. io1: 260,000 IOPS and 7,500mbps throughput. That is 4 volumes of io1
2. io2: 160,000 IOPS and 4,750mbps throughput.  
3. block express: 260,000 IOPS and 7,500 mbps throughput. 

### HDD based