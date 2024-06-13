# TOPICS COVERED
- [Bootstraping EC2 using User Data](#bootstrapping-ec2-using-user-data)
- [EC2 Instance roles and profiles](#ec2-instance-roles-and-profiles)
- [SSM Parameter Store](#System-Manager-Parameter-Store)
- System and Application Logging on EC2
- EC2 placement groups
- Enhanced networking and EBS optimized

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