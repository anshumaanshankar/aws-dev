# TOPICS COVERED
- [Bootstraping EC2 using User Data](#bootstrapping-ec2-using-user-data)
- [EC2 Instance roles and profiles](#ec2-instance-roles-and-profiles)
- [SSM Parameter Store](#System-Manager-Parameter-Store)
- [System and Application Logging on EC2](#system-and-application-logging-on-ec2)
- [EC2 placement groups](#ec2-placement-groups)
- [Enhanced networking and EBS optimized](#enhanced-networking-and-ebs-optimized-instances)

## Bootstrapping EC2 using User Data
In the context of EC2, bootstrapping would involve setting up instances with software installations and configurations needed, right at the time of creation. 

We can do this with user data, which is a piece of information we pass to the instance. The user data is executed only at launch time and can be accessed from the metadata IP as `http://169.254.169.254/latest/user-data`. Anyone with access to the user OS can access the user data. 

### Architecture
- Launch an EC2 instance using an AMI, giving it a EBS volume.
- The EC2 service provides user data to the instance, whose software looks for this user data upon creation. If any exists, its executed.
- Instances will be in running state and pass status checks, even if the user data execution was not successful. This would be a `bad configuration`. 
- User data is <= 16kb by default. 
- User data can be modified after launch, but these changes will not be implemented even on restarting an instance. 

### Boot Time to Service Time
- The time taken from launch till availability of the instance. 
- `Time to provision an instance + Post launch time (time taken to update software and configs).`
- Time to provision an instance is usually in minutes. Post launch time depends on whether the update is manual or automated.
- Using AMI Baking for time intensive processes and bootstrapping final configs is the most time efficient way to reduce post launch time. 

## EC2 Instance roles and profiles
- Instance roles allow applications in an EC2 instance to access AWS resources.
- An instance profile is a wrapper for an instance role, that delivers these permissions to the instance. 
- An instance profile what gets attached to an instance. The temporary security credentials are delivered through the instance meta-data to the instance.
- Applications running in an instance can use these credentials, to access AWS resources. These security credentials are always renewed before they expire.  
- Roles are preferable whenever possible, never store long-term credentials. 
- DEMO:
    - Make an EC2 instance, an IAM role that has necessary permissions assigned
    - Attach to EC2 instance by right click >> security >> modify IAM role.

## System Manager Parameter Store
- SSM parameter store allows for storing configurations and secrets (License codes, passwords etc) using strings, stringlists and securestrings as both plaintext and ciphertext (using KMS).
- Parameter store allows for versions and has a hierarchical structure. 
- They also allow for public parameters.
- Storing and changing parameters can result in events that lead to changes on AWS products. 

## System and Application Logging on EC2
 The inside of an instance is opaque to these, by default - We cannot directly access the metrics and logging data of EC2 instances.

### Logging for EC2 instances
- Using a cloudwatch agent running on the instance with necessary permissions and configs, we can send this data to CW and CW logs.

### Architecture
- Install CW agent inside the instance, and provide it with information of what data to send to CW.
- Give the agent permissions (using a role) to interact with CW and CW logs. 
- Configure log groups and log streams on CW to organize data properly.
- At scale, we can automate providing a CW agent and its working by using CloudFormation.

## EC2 placement groups
Placement groups determine how instances are aligned on hardware. There are three types:
1. Cluster placement groups - pack the instances close together. `Performance optimized`.
    - Launch similar instances, all at the same time. This way, all instances are launched in the same AZ.
    - Instances share a rack - networking, power supply etc.
    - AZs are locked on launch, we cannot span. We can span VPCs but this affects performance. 
    - Members have direct connections to one another, with the DTR being faster (10gbps as opposed to 5).

2. Spread placement groups - Keep instances seperated and on different hardware. `Resilience optimized`.
    - Span multiple AZs (max of 7 instances per AZ), each instance has a distinct rack. 
    - We have isolated infrastructure, network and power source.
    - Best practice: Have a small number of critical instances that need to be separated from one another. 
    - `Cannot use dedicated hosts and instances.`

3. Partition placement groups - Multiple groups of instances, each group on different hardware. `Topolgy awareness optimized`.
    - Used when we have more than 7 instances per AZ, but you need to separate them.
    - Max of 7 partitions per AZ, each partition has independent infrastrcture and racks. 
    - Unlimited number of instances per partition (We can pick or let EC2 to pick).

## Enhanced networking and EBS optimized instances
### Enhanced networking
- Employs SR-IOV which enables a physical network interface inside an EC2 host to realize its in a virtualization.
![alt text](<Screenshots/Screenshot 2024-06-13 at 7.07.28 PM.png>) ![alt text](<Screenshots/Screenshot 2024-06-13 at 7.07.40 PM.png>)

In fig 1, the Network interface card (NIC) is unaware of virtualization, so the host has to ensure that only one instance is using it at a time. In the second example, We use SR-IOV which lets each instance have an independent NIC. This allows for:
1. Higher I/O, Lower Host CPU usage
2. More Bandwidth
3. Higher Packets-per-second (PPS)
4. Consistent low latency

### EBS optimized instances
EBS optimized instances have dedicated capacity provided for EBS (For storage networking). It is supported and enabled by default on most instances, we have to pay to enable it on some other instance types.