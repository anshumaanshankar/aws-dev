## TOPICS COVERED
- Instance Store volumes
- Choosing between Instance store and EBS.
- Snapshots, Restore, Fast Snapshot restore (FSR)
- EBS Encryption
- Network Interfaces, Instance IPs and DNS
- Amazon Machine Images (AMIs)

## Instance Store Volumes
- <ins>Temporary</ins> Block storage connected physically to an EC2 host. One block (or volume) is attached to 1 EC2 instance. 
- Paid for with the instance. No excess $$.
- Volumes must be attached to an instance when it is launched, cannot be attached later. Based on instance type, no. volumes, volume type changes. 
- If an instance moves hosts, their data stored on a previous instance volume is lost.
- If a physical volume fails, the instance associated with that volume loses that data. 

`Performance`: Owing to the physical link, instance store volumes perform better in terms of throughput and IOPS compared to EBS.

## Choosing between Instance store and EBS
EBS                                             |   Instance Store
------------------------------------------------|------------------
Better for resilient storage                    |   Cheaper
Better for Persistent storage                   |   Super high performance
Storage isolated from instance lifecycles       |   More than 260,000 IOPS for application

### Other Considerations
![alt text](<Screenshots/Screenshot 2024-06-05 at 10.39.25 AM.png>)
*io2 block express

Unclear:
1. Resilience within the app with supports built in replication

## EBS Snapshots, Restore, Fast Snapshot restore (FSR)
### Snapshots and restore
- Backups of EBS volumes stored in S3, making the data region resilient. 
- First snapshot copies all data onto S3, future snapshots are incremental (changes since last snapshot are added).
- EBS volumes can be created/restored from snapshots: enabling data migration b/w regions. 
- When volumes are restored from a snapshot, data is transferred in the b/g over some time. 
- Requested blocks that are not present on the volume yet will instantly fetch from S3 - lower performance.
- We can force a read of all block of the volume, forcing all snapshot data to be pulled into the volume from S3. 
- Billed on gb-month basis (1gb per month = 2gb per half month)
- Snapshots are only billed for their size. Eg - Snapshot 1 contains 10gb of data. Snapshot 2 is incremental and incorporates 4gb of changes in the original => snapshot 2 is only billed for 4gb. 

### Fast Snapshot Restore
- instantly restore 
- 50 instant restores can be done on a region by picking the specific snapshot and the AZ we want to restore to. Restoring 1 snapshot to 4 AZs uses 4 of those 50.
- Performs the same operation of forcing a read, but costs extra. 