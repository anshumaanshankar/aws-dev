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
- deny list: When an SCP is enabled, a fullAWSAcess is automatically applied across the org. Meaning, we have full access atp. We have to explicitly deny something to prevent usage of something. 
- allow list: This list has no fullAWSAccess on creation, meaning we initially start with implicit denies for everything. Anything needing access requires an explicit allow. <ins>More admin overhead</ins>.

Benefits of deny list over allow is that we would have to allow access one by one as more services are made. Having the fullAWSAcess 

