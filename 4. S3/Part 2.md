# TOPICS COVERED
- S3 Replication
- Presigned URLs
- Select and glacier select
- Cross Origin Resource Sharing (CORS)
- Events
- Access Logs
- Requester Pays
- Object Lock

## S3 Replication
- Replication of buckets between source and desination bucket(s)
- Two types of replication: Cross region replication (CRR), Same region replication (SRR).
- Replication configuration - configures s3 to replicate object(s) from source to destination. You have to set the destination, an IAM role for the process (trust policy allows access by account, permisisons policy allows replication and storage on destination). 
- Only difference b/w replication between same and different accounts is that we have to add a bucket ploicy at the destination that trusts the IAM role in the source. This is not needed for same account.  

![alt text](<Screenshots/Screenshot 2024-05-24 at 1.28.06 PM.png>)

- **Replication Configuration options**:
    - What to replicate: All objects or subsets of the source bucket
    - Which storage class to use in destination: default is the same class as source, but a cheaper class can be picked  (because this is a copy)
    - Who owns the objects: default is the source account. Can be changed to destination account.
    - Replication Time Control (RTC): ensures that the buckets are in sync within a 15 min window. 

- **Conditions: IMPORTANT**:
    - replication is not retroactive by default, you're asked if you want to at the time of creating the replication rule.
    - Versioning must be on
    - Batch replication can be used to replicate existing objects
    - Source to Destination replication does not work in the reverse by default. Bi-directional replication is an additional setting that should be configured for this.
    - Replication is possible on all encrypted data types. (KMS encryption requires extra configurations)
    -  Source bucket owner must have permissions to the object. 
    - Glacier storage items can't be replicated
    - No system events can be replicated
    - Deletion is not replicated (adding delete markers) by default. This can be enabled.

- **Why replication**:

    ![alt text](<Screenshots/Screenshot 2024-05-24 at 1.38.53 PM.png>)

## Presigned URLs
- A way to give a person/object access to an object in s3 using the authorized user's credentials. 
- An authorized user can ask S3 to create a presigned URL, that can be given to the person/object we need to give access to, along with expiry time requested by the authorized user.
- When the URL is valid, the person can operate the object *as* the person who asked for the URL to be made, meaning they have that person's permissions. The URL will not work if the necessary permissions are removed on the creator, even after the URL is made. 
- Do not make presigned URLs using IAM roles (expiration is longer on the URL compared to the role)
- **CASE**: Consider we have a web application that displays video content stored on S3. Using presigned URLs, we can keep our bucket private while providing access of that video to people. By creating an IAM user for the application, the application can initiate a request to S3, asking for a presigned URL that allows users of the application to see, download the video they wish to. 

The image below shows how a user is able to access and download the video stored on an S3 bucket using presigned URLs by following the previously mentioned steps.


![alt text](<Screenshots/Screenshot 2024-05-24 at 2.50.18 PM.png>)

## S3 Select and glacier select
Using SQL-like statements, <ins>we can retrieve parts of an object</ins> - pre filtered by S3 (a lot of time, only a part is needed).

### Comparing architectures
![alt text](<Screenshots/Screenshot 2024-05-24 at 3.25.58 PM.png>)