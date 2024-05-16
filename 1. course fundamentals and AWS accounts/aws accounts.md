An AWS account is a container for users and resources. Each account is made with an account name, email ID and credit card.

the email is used to provide a special user - the root user of an account.

If we make an account, that account's root user can alone log into that account.

the account root user is initially the only user for an AWS account. this person has full control over that account and its resources.

we can make additional identities / users that can be restricted for an account using IAM. The IAM service is dedicated to the account (that is, any identities created on an account cannot access another). It is a best practice to make additional identities / users to use the account. Using the root user is not ideal, as they have no restrictions. 

