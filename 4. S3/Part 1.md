# TOPICS COVERED on S3
- Security
- Static Hosting
- Object versioning and MFA delete
- Performance optimization
- Key Management Service (KMS)
- Object Encryption CSE/SSE

## Security
- Initially, only the root user has access to S3. 
- Ways to grant permission to S3:
    1. S3 bucket policy: 
        - A form of resource policy (policy attached to a resource, as opposed to an identity).
        - Resource policies can allow access to a resource by identities in the same account, or a different account.
        - Can allow/deny anonymous principals (those not authenticated by AWS)
        - **Resource policies have a "principal" attribute, identity policies do not.**
        - We can only have one policy on a bucket, but we can have many statements in that policy. 
        - Access by AWS identities (both same and cross account) is dependent on both the individual's identity policy and the bucket policy. 
    2. Access Control Lists (ACLs):
        - Less flexible compared to resource/identity policies (only 5 functions).
    3. Block public access settings:
        - Apply only to anon. principals attempting to access a bucket.
        - Overrides resource policies if you choose to block all access.
        - Options to block access granted by ACLs (One option per permutation on existing and new ACLs)

## Static Hosting
- This allows access via http (great for stuff like blogs)
- We need an index (pointed at an HTML object in s3 buckets) and error documents 
- This enables a website endpoint is created, whose name depends on the bucketname and the region
### Common uses of S3 in websites:
1. Offloading: Storing media in an S3 bucket as opposed to a compute service. This saves $$.
2. Out of band pages: Hosting pages (such as maintanence) on S3, as opposed to the server where the website is hosted. By changing the DNS, we can point viewers at this backup website if the server is down. 

### Fees on S3:
0. 5 gb of free storage, 20,000 get requests and 2000 put requests for free.
1. Storing data, cost is per GB
2. Transferring data into and out of a bucket , cost is per GB
3. Requesting data from S3, cost is per 1000 operations

