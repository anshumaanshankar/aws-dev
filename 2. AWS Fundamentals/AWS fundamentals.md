# TOPICS COVERED
- Public vs Private Services

## Public v Private Services
- **TLDR**: There are 3 different network zones: Public internet, AWS public zones, AWS private zones. AWS public zones host services that are public (S3) and AWS private hosts VPCs. 

- Public services are accessed from public endpoints (example, S3 buckets can be accessed from anywhere with an internet connection) with necessary permissions.
- Private Services runs within a VPC, and can only be accessed from within that VPC or by things connected to the VPC.

- AWS has public zones that run between the private zone of AWS and the public internet. This is a network connected to the public internet, but is not a part of it. This is where AWS public services like S3 operate.
- AWS has private zones (Virtual Private Clouds). Access is not automatic from outside unless provided. Even internet access has to be allowed explicitly.



![alt text](<Screenshot 2024-05-17 at 1.59.00 PM.png>)