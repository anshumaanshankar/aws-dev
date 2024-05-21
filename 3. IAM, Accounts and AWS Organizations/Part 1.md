## TOPICS COVERED
- IAM identity policies
- IAM users and ARNs
- IAM Groups
- IAM Roles
- When to use IAM Roles

## IAM identity policies
- policies are JSON documents made of permission statements attached to users, groups and roles. Each statement includes:
    - Sid: informs the reader of what that statement does
    - Effect: Allow or deny. This controls what AWS does when the resource and action match.
    - Action: Action(s) performed on a resource. Using * means we can do all actions possible.
    - Resource: The resource we are allowing/deny access to, specified by ARN
    
Different statements may allow and deny an action at the same time. In this case, the priority of effects come into play:
- Explicit deny
- Explicit allow
- Default deny 

![alt text](<Screenshots/Screenshot 2024-05-20 at 3.50.17 PM.png>)

In the above image, User is allowed access to all S3 buckets except catgifs, due to the explicit deny.

If a user's access to a resource is dependant on multiple policy documents, they are all evaluated together. Even one explicit deny => game over.In the absence of any explicit deny, 1 or more explicit allows is sufficient to get access (explicit allow > implicit/default deny)

- Managed Policies: To add a managed policy to a user, we would select it from the list of policies we see when we are on the "permissions" tab for a user. GO to IAM > policies and create one for a custom policy. <ins> They're reusable, low management overhead. </ins> 

- Inline Policies: To add an inline policy, we have to create an inline policy on the permissions tab for a user and input the JSON text. We would do this one by one for each user. <ins>Better used for exceptions to managed policies.</ins> 

## IAM users and ARNs
**IAM Users**: A principal needing long-term AWS access (people, applications, service accounts).

- **Authentication** through username/password or access keys proves to IAM that the principal (person, device or process) is authorized. 

- **Authorization** is IAM checking an individual's statement to allow or deny access to resources.

**ARNs**: Uniquely identify resources in an AWS account. The general format of an ARN is shown below. For standard AWS, partition name is always AWS. Exceptions are in China where the partition would be AWS-CN for example. 
![alt text](<Screenshots/Screenshot 2024-05-20 at 4.23.34 PM.png>)

The two ARNs shown below are different. The first accesses only the bucket, the second accesses only the objects inside it.

![alt text](<Screenshots/Screenshot 2024-05-20 at 4.21.30 PM.png>)

Two create/modify a bucket AND its objects, we would need both these ARNs. The double colons indicate that those parameters need not be mentioned explicitly (as S3 buckets are globally unique). 

## IAM Groups
- Organizers or containers for IAM users. <ins> They cannot be logged into, have no credentials. </ins>
- A user can be in upto 10 groups, but a group may have as many users as needed (5000 max users per account)
- We can attach inline/managed policy to groups as well.
- We cannot have groups inside groups. 
- 300 groups per account (soft limit)
- Groups cannot be referenced as a principal in a policy. That is, Resource access cannot be granted on groups.

## IAM Roles
- Best for multiple principals (like many users/service accounts in the same aws user).
- A role represents a level of access inside an AWS account. Mostly used short term by other identities for borrowed permissions.
- IAM roles have two types of policies:
    - trust: Determines which identities can assume that role. This may be users(people), services(EC2) in the same or other AWS accounts. Temporary credentials are given to identities that assume the role, created by Secure Token Service (sts).
    - permissions: the accesses given/not given to identities assuming that role.

## When to use IAM roles
- Used when some services need access permissions to perform their job. 
    - For example: AWS Lambda, basically a lambda function that does things based on code, may need access to EC2 instances to shut them down. For this, we would assign it a role that gives it the security credentials and permissions needed to access EC2. 
- External accounts cannot directly access AWS (like google, facebook accounts). Allowing an IAM role to be assumed by these external identities permits temporary access to allowed resources. 
- Break glass situations: When some emergency leaves you needing more permissions than normally given to you.
- If we have more than 5000 accounts (max no. IAM users for an account), we can have roles granting permissions as necessary. This is easier to manage compared to a large no. users. 
- If we are designing a mobile app with large no. users, We would use roles that allow access to necessary resources for the apps that are stored on AWS. This is done through allowing web identity federation (ID federation) - logging in using facebook/google accounts. Advantages of using roles in this case are:
    - No AWS credentials stored 
    - Uses existing customer login
    - Scales to high no. users.
- cross-account access:
![alt text](<Screenshots/Screenshot 2024-05-21 at 11.44.12 AM.png>)
