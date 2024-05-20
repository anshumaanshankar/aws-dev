## TOPICS COVERED
- IAM identity policies
- IAM users and ARNs

## IAM identity policies
policies are JSON documents that are a set of security statements attached to users, groups and roles. Policies are made of statements. Each statement includes:
    - Sid: informs the reader of what that statement does
    - Effect: Allow or deny. This controls what AWS does when the resource and action match.
    - Action: Action(s) performed on a resource. Using * means we can do all actions possible.
    - Resource: The resource we are allowing/deny access to
    
    Different statements may allow and deny an action at the same time. In this case, the priority of effects come into play:
    - Explicit deny
    - Explicit allow
    - Default deny 

![alt text](<Screenshots/Screenshot 2024-05-20 at 3.50.17 PM.png>)

In the above image, User is allowed access to all S3 buckets except catgifs, due to the explicit deny.

If a user's access to a resource is dependant on multiple policy documents, they are all evaluated together. Even one explicit deny => game over.In the absence of any explicit deny, 1 or more explicit allows is sufficient to get access (explicit allow > implicit/default deny)

- Managed Policies: 1 JSON we can apply on any number of identities needed. <ins> They're reusable, low management overhead. </ins> AWS managed policies are made by AWS, and we have custom ones too. 
- Inline Policies: Applying a policy to each individual account that matters. If we have 3 identities, we would have 3 separate policy documents. <ins>Better used for exceptions to managed policies.</ins> 

## IAM users and ARNs
**IAM Users**: Anything needing long-term AWS access (people, applications, service accounts).

**Authentication** through username/password or access keys proves to IAM that the principal is authorized. 

**Authorization** is IAM checking an individual's statement to allow or deny access to resources.

**ARNs**: Uniquely identify resources in an AWS account. The general format of an ARN is shown below. For standard AWS, partition name is always AWS. Exceptions are in China where the partition would be AWS-CN for example. 
![alt text](<Screenshots/Screenshot 2024-05-20 at 4.23.34 PM.png>)

The two ARNs shown below are different. The first accesses only the bucket, the second accesses only the objects inside it.

![alt text](<Screenshots/Screenshot 2024-05-20 at 4.21.30 PM.png>)

Two create/modify a bucket AND its objects, we would need both these ARNs. The double colons indicate that those parameters need not be mentioned explicitly (as S3 buckets are globally unique). 
