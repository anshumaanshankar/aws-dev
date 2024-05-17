## IAM - Identity and Access Management

The account root user is made w the account, is not resricted in any way.
Mostly, we need other people using our aws account but obviously we cannot give all the access.
This is called least previlege access. 
IAM is used to see what user/identity has what access. 

## IMPORTANT: IAM is globally resilient, meaning any data is secure across all AWS regions.

IAM, like the root user, can do almost anything (except billing and all). As a service, it can do as much as the user.
IAM is used to make diff types of identities and IAM gives these identities permissions to do certain things. Thus, the account allows that identity to do those things.

IAM lets us make 3 types of identities - users, groups and roles. 

- Users: humans or apps that need access to AWS accounts. individual person or thing that needs access to something.
- Groups: collections of related users. For example, finance and HR users can be grouped together
- Roles: used by AWS services, or for externals to use our account. They are generally used to grant access by a service to an unspecified / unknown no. entities. Used to grant an uncertain number of persons or things an access to something (users allow access to individuals, roles allow for many users)

IAM policies or documents can be used to deny access to services when attached to a user or role. 

Always make an admin user after making an AWS account - never use the root user for daily usage. 