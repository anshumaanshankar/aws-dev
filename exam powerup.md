# POINTERS FOR THE EXAM
## General
- ARNs uniquely identify a resource within AWS.
- AWS are responsible for the security of the cloud, the user/customer is responsible for security in the cloud. 
- Policy document priority: explicit DENY, explicit ALLOW, default DENY.
- cloudtrail is NOT REALTIME, there may be a 15 min delay for data to appear in s3 or cw logs. 

## S3
- object sizes can be 0 to 5TB on S3.
- Bucket names are globally unique.
- all bucketnames should be 3 to 63 chars. lowercase or number to start.
- 100 buckets per aws account (soft limit) upto 1000 buckets (hard limit). If we have a lot of users, we can have prefixes to indicate the owner of content in a bucket, thereby allowing many users to use a single bucket.
- For working inside one account, identity policies are better than bucket resource policies. When we want to control access to a bucket or object by identities from diff accounts, bucket policies are better. 
- Versioning on a bucket, once enabled can never be disabled. Only suspending a bucket is possible atp.
- S3 buckets having dots in their name and not conforming to DNS norms cannot be transfer accelerated. 
- KMS provides a FIPS 140-2 Level 2 security standard.
- AES-256 is the Encryption type on S3 for SSE.
- You can make presigned URLs for objects that you don't have access to, but the URL does not allow access either (because you don't have access).


## IAM
- IAM is globally resilient, meaning any data is secure across all AWS regions.
- max of 5000 IAM users per account. For large no. users (eg. large companies / merges) we use IAM roles, not one user per person.
- an IAM user can only be in <= 10 IAM groups.
- Usually, an identity policy that has only deny statement(s) are applied in conjunction with other policies that have allows. To apply just this policy to a user or role is useless.