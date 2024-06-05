# TOPICS COVERED
- Virtualization 101
- Architecture and Resilience
- Instance Types
- Storage Refresher
- ELastic Block Store (EBS) Service Architecture

## Virtualization 101
**Definition**: Running more than one operating system on a piece of hardware. Allows multiple "previleged applications" to run on the same hardware. That is, diff apps on diff operating systems. 

Types of Virtualizations:
1. Emulated Virtualizations (Or software virtualization): Hosting multiple "guest" operating systems on virtual machines, that believed they ran on real hardware, thus their apps were previleged. These are connected to the actual hardware using the host operating system / hypervisor. These operating systems are using memory and disk on local hardware, thinking its their own. The hypervisor performs "binary translation" - used to prevent one guest OS's content being overwritten by another etc. <ins>Very slow for demanding workloads</ins>

2. Para virtualization: Only some Operating systems can be used on virtual machines for this. In this method, areas of the guest OS that usually make calls to the hardware, now make "hypercalls" to the hypervisor in use. By doing this, we improve performance. 

Both 1 and 2 have a set up that looks like this.
![alt text](<Screenshots/Screenshot 2024-06-04 at 1.38.30 PM.png>)

3. Hardware assisted virtualization - The hardware itself is aware of the virtualization. The CPU expects instructions from the guest OS, which is redirected to the hypervisor which handles the execution. This is even faster. **Drawback**: The VM still think they have actual hardware, when they don't - they actually use cards on the hypervisor. This means, when we perform high transactional activities, performance is impacted. Lot of CPU cycles are consumed on the host.
4. 
![alt text](<Screenshots/Screenshot 2024-06-04 at 1.48.08 PM.png>)

5. SR-IOV (Single Route IO Virtualization) - Allows add-on cards to present itself as several mini-cards that are unique. These are presented to the OS, dedicated for its use. Thus, the guest OS can use these cards directly without needing the hypervisor. <ins> Used for advanced networking now.</ins> 
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
- Throughput: Rate at which data can be stored on disc.
All three metrics are related. 
        _`Possible throughput = block size * IOPS.`_
**Systems might have throughput caps, or as the block size is increased, IOPS may decrease.**

