# TOPICS COVERED
- AWS Organizations
- Demo
- Service Control Policies
- CloudWatch logs
- CloudTrail

## AWS Organizations
Helps us deal with having many AWS accounts in the same company
- Take one AWS account (standard account) that is not in an org. and make an org. with it
- This std. account becomes the management account / master account
- We can invite existing accounts using the management account or make new accounts inside it with an email address having no AWS account. 
- When accounts join, they become member accounts. 
- Consolidated billing: All the billing costs for member accounts are passed on to the management account (aka the payer account).
- Some services offer cheaper rates for larger use. Thus, having organizations may cut costs for using services.
- Using roles, we can achieve cross-account access inside an organization.

- AWS ogranizations have a hierarchical structure:
    - First level: Organization root - contains member/management account. <ins> NOT THE SAME AS ACCOUNT's ROOT USER</ins>
    - Lower level: Organizational unit (OUs) - contains member/management account or other OUs (nesting).
    - Everything is under the organization root (like in a tree's root node). OUs can have AWS accounts, or other OUs inside them.


## DEMO ON MAKING ORG, ROLE SWITCHING
1. Log into general-admin, go to aws organizations and make one
2. Using the production's acc ID, invite it to the org and accept invite from production
3. To enable role switching (accessing prod from general), On Production do the following:
    - go to IAM >> roles
    - create role with trusted entity being AWS account. Select "another AWS account" and enter general's account ID. (Trust policy for the role)
    - Give necessary permissions (Permissions policy for the role)
    - Enter role name and create
4. On General, hit the drop down on the top right next to name, select "switch role"
5. Enter the account ID of the production account, the same role name given in step 3
6. Now we can switch from inside general, into production.
7. When we make new accounts inside an org, a role is made automatically, allowing us to access that new account from the management account.

## Service Control Policies
- Service Control Policies (SCPs) are policy (JSON) documents attached to the organizational root, one or more OUs, or individual AWS accounts. They inherit down the tree. Thus, attaching to the root will impact all accounts in the org. 
- Management account is never affected by the SCP, even when the SCP is attached to the org root or itself.
- SCPs limit what member accounts can do (thereby nerfing the account's root user indirectly - think of it like the SCP removing certain functions as though they never existed from the account). 
- <ins>They follow the same deny-allow-deny rule. </ins>

#### Allow vs Deny List
- deny list: When an SCP is enabled, a fullAWSAcess is automatically applied across the org. Meaning, we have full access atp. We have to explicitly deny something to prevent usage of something. An example of a deny list is present at [this file](deny_list.json).

- allow list: This list has no fullAWSAccess on creation, meaning we initially start with implicit denies for everything. Anything needing access requires an explicit allow. <ins>More admin overhead</ins>.

Benefits of deny list over allow is that we don't have to allow access one by one as more services are made. the fullAWSAccess takes care of that for us.  

## Cloudwatch Logs (cw logs)
- public service that allows services to store, monitor and access logging data (information data with timestamp)
- Integration with some services (EC2, Lambda etc). These services automatically store data with cloudwatch logs.
- Using the unified cloudwatch agent, we can log data into cloudwatch from external services.
- They can generate metrics based on the logs (Metric filter).

![alt text](<Screenshots/Screenshot 2024-05-21 at 5.09.58 PM.png>)

The architecture of cloudwatch is given above. All log events from the same source (say, an EC2 instance) are all stored in a logstream. We group same kind of information from various sources (different EC2 instances, say) in a log group. Log groups are where settings (retentions, permissions), metric filters are defined. These metric filters can be attached to alarms. 

## Cloudtrail
- Logs activities that interact with AWS services, each activity is called a cloudtrail event
- 90 days worth of events are stored in Event history at no cost
- We can create a trail to hold on to events for more than 90 days (put them in s3 or cw logs)
- A trail logs events in an AWS region where its created, unless it is set to log events in multiple / all regions. 
- Management and data events (if enabled) are captured by a trail. 
- When we create a trail, we can store events in S3 buckets and we are only charged for the storage. 
- Cloudtrail events can also be stored on cloudwatch logs, which we can use to search through it or use metric filters to take advantage of the data. cloudwatch logs is better than S3.
- We can create a trail from the management account of an org to store all events of all accounts in that org.
- True global services such as IAM, STS have events called global services events, which are stored in us-east-1 by default. 