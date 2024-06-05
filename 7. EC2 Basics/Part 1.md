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
- A primary elastic network interface (ENI) is provisioned in a subnet and mapped to the EC2 host.
- An ENI enables the EC2 instance with network connectivity, aka the ability to communicate within that subnet and, given permissions, outside of it.
- An EC2 instance can connect to a host only from an ENI of its own subnet. ENIs from multiple subnets can establish connections to a host. 
- Instances of the same type and size mostly share the same host. 
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
- Each block is called a `volume`.
- EBS volumes can only be attached to one instance at a time, but we can detach from one and attach to another.
- Instances see a block device on top of which they make file systems. 
- EBs volumes are local to one AZ. To share across AZs or regions, we can take a "snapshot" of the volume.
- Billed at gb-month (1gb per month = 2gbs per half month).

![alt text](<Screenshots/Screenshot 2024-06-04 at 6.33.14 PM.png>)
<p> We have 2 AZs in us-east-1, 1 AZ in ap-southeast-2. The EBS in each AZ are independent. Snapshotting volumes allow usage in different AZs, regions. EC2 instances cannot connect to EBS volumes in different AZs or regions. 

## EBS Volume Types
### General Purpose SSD - GP2
- A GP 2 volume can be b/w 1gb and 16tb in size.
- Max. throughput of GP2 is 250mbps. 
- Each volume comes with an 'IO Credit Bucket' with 5.4 million initial IO credits, each capable of moving upto 16kb. The bucket refills at or over baseline performance. 
- `Baseline performance: IO credits incoming per second (IOPS)` is 100. If the volume size is <= 33.3gb, we get 100 IOPS. For volume > 33.3gb, we get 3 IO credits per gb per second. That is, for a volume of size 70gb, we would get 70*3 = 210 IOPS. 
- Baseline performance is capped at 16,000 IOPS. The limit is reached at 5.33tb (3 IOPS * 5.33 tb = 16k IOPS)
- `Burst: Maximum IOPS outgoing` is 3000. Bursting empties our credit bucket.
- At 1tb, the number of credits entering per second exceeds the burst of 3000 IOPS.

### General Purpose SSD - GP3
- `20% cheaper than gp2, mostly even when paying extra`.
- Size of the volume is b/w 1gb and 16tb.
- No credit architecture system like in GP2. 
- Each volume, regardless of size, has baseline performance of 3000 IOPS and throughput of 125 mbps.
- Both baseline performance and throughput can be increased by paying extra - upto 16,000 IOPS and 1000mbps.
- There is no specified burst.

### Provisioned IOPS SSD - io1 io2, io blockexpress

Metric                  | io1           | io2           | Block express
--------------------    |---------------|---------------|---------------
Size                    |4gb-16tb       |4gb-16tb       | 4gb-64tb
Baseline performance    |50 IOPS per gb |500 IOPS per gb| 1000 IOPS per gb
Max baseline performance|64,000 IOPS    |64,000 IOPS    | 256,000 IOPS
Max throughput          |1000mbps       |1000mbps       | 4000mbps

`Per instance performance: The maximum performance that can be acheived b/w EBS service and an EC2 instance.` It is influenced by volume type, instance type and instance size. 

Assuming ideal type and size of instance; We can see max per instance performance of:
1. io1: 260,000 IOPS and 7,500mbps throughput. That is 4 volumes of io1
2. io2: 160,000 IOPS and 4,750mbps throughput.  
3. block express: 260,000 IOPS and 7,500 mbps throughput. 

### HDD based
1. Throughput optimized (st 1):
    - Designed for sequentially accessed data
    - Range from 125gb to 16tb
    - Max of 500 IOPS. I/O is measured as 1mb blocks.
    - Baseline throughput of 40mbps per TB, scalable upto 250mbps per TB.
    - Designed for Big Data, Data warehouses and log processing
2. Cold HDD (sc 1):
    - Designed for infrequent workloads.
    - Range from 125gb to 16tb
    - Max of 250 IOPS with 1mb I/O
    - Baseline thorughput of 12mbps per TB, scalable upto 80 mbps per TB
    - `Lowest cost storage in EBS`
    
