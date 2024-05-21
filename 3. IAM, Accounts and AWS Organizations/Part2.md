## TOPICS COVERED
- AWS Organizations
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
    - First level: Organization root - contains member/management account. <ins>NOT THE SAME AS ACCOUNT's ROOT USER</ins>
    - Lower level: Organizational unit (OUs) - contains member/management account or other OUs (nesting).


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
7. When we make new accounts inside an org, a role is made automatically, allowing us to access that new account from the management account
