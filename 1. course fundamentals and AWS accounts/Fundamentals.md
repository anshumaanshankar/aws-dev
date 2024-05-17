## TOPICS COVERED
 - AWS accounts
 - IAM
 - MFA
 - Access keys
 - using AWS via Command Line Interface

## AWS accounts

- An AWS account is a container for users and resources. Each account is made with an account name, email ID and credit card.

- The email is used to provide a special user - the root user of an account.

- If we make an account, that account's root user can alone log into that account.

- The account root user is initially the only user for an AWS account. this person has full control over that account and its resources.

- We can make additional identities / users that can be restricted for an account using IAM. The IAM service is dedicated to the account (that is, any identities created on an account cannot access another). It is a best practice to make additional identities / users to use the account. Using the root user is not ideal, as they have no restrictions. 

## IAM - Identity and Access Management

- The account root user is made w the account, is not resricted in any way.
- Mostly, we need other people using our aws account but obviously we cannot give all the access.
- This is called least previlege access. 
- IAM is used to see what user/identity has what access. 

### IMPORTANT: IAM is globally resilient, meaning any data is secure across all AWS regions.

- IAM, like the root user, can do almost anything (except billing and all). As a service, it can do as much as the user.
- IAM is used to make diff types of identities and IAM gives these identities permissions to do certain things. Thus, the account allows that identity to do those things.

- IAM lets us make 3 types of identities - users, groups and roles. 
    - Users: humans or apps that need access to AWS accounts. individual person or thing that needs access to something.
    - Groups: collections of related users. For example, finance and HR users can be grouped together
    - Roles: used by AWS services, or for externals to use our account. They are generally used to grant access by a service to an unspecified / unknown no. entities. Used to grant an uncertain number of persons or things an access to something (users allow access to individuals, roles allow for many users)

- IAM policies or documents can be used to deny access to services when attached to a user or role. 

 - Always make an admin user after making an AWS account - never use the root user for daily usage. 

## MFA

### why do we need MFA?
- if username and password are leaked, anyone can be you
- factors are used to prove evidence
- factors: knowledge (username, password) , possession (something you have, like an MFA device),
inherent (fingerprint, face scan, voice scan), location(physical or network location)
- the more factors, more the security.

### how does AWS handle a second factor for log in?

- by default, we use knowledge (username and password). This is single factor auth
- to prevent any problems caused by leak, we use MFA
- we use a physical or virtual MFA device like google authenticator


## ACCESS KEYS

- Access Keys are long term credentials that can be used to login as IAM user through the command line
- other long term credentials are username and password etc
- an IAM user can have two access keys that can be made, removed, rendered inactive or active
- The access key is made of the access key ID and secret access key, both of which are given at the time of creation to you. Secret access key should be noted down at the time of creation. Secret access keys cannot be changed. If leaks are feared, the entire key needs to be deleted.
- when we make a request using the command line to AWS, we need to use both parts of the access key
- Rotating access keys means to create new access keys and remove the old ones (periodically?)
- IAM users are the only identities that use access keys (roles do not), except for the root user. Using these for root though is not advised 


### - credential leak: when both username and password are leaked. username alone is not a leak


## ACCESSING AWS VIA CLI

- After installing, we can use the following command to configure a profile:
    - aws configure --profile *profilename*

- We can check for any existing s3 buckets in a given profile using the command:
    - aws s3 ls --profile *profilename*