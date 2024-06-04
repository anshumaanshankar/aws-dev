# TOPICS COVERED:
- Stateful v Stateless Firewalls
- Network Access Control lists (NACLs)
- Security Groups
- Network Address Translation (NAT) and NAT gateway

## Stateful v Stateless Firewalls
- When a connection is made from a computer to a website,
    1. A **request** is made from a ephemeral port on the user's laptop (port no. b/w 1024 and 65535) and is sent to a destination port no. that's well known (for example, port 443 is https) along with the request for whatever data.
    2. A **response** is sent back by the server, having source address as that same 443 and the destination being the ephemeral port number.
- Requests can be both incoming or outgoing. Whatever the direction of a request, the direction for the response is opposite.

### Stateless Firewalls
- Stateless firewalls do not evaluate request-reponse together. 
- The request and response for the same connection will require separate rules (one inbound, one outbound). 
- On stateless firewalls, we need to allow the full range of ephemeral ports on outgoing responses.

### Stateful Firewalls
- Identifies request-response as pairs.
- Only one allow/deny is needed for a request (regardless of inbound or outbound), depending on which the response is also understood. 
- We don't need to allow the full ephemeral port range. 

## Network Access Control lists (NACLs)
- Every subnet has a NACL, which filters data as it crosses the boundary of the subnet: connections within a subnet are not affected.
- NACLs have a set of inbound rules and a set of outbound rules to explicitly allow/deny on requests and responses.(Inbound/outbound can request/response they are stateless).
- While communicating with services on different subnets, all NACLs will need the appropriate permissions. 
- Rules match destination IP range, port number or protocols to decide an allow/deny. <ins>DENY-ALLOW-DENY applies.</ins>
- Each rule has a number, rules are evaluated bottom up.
- If a rule number is a *, its the default rule that is applied when no other match is seen. Most often, its a deny. 
- Commonly used with security groups to explicitly deny.
- VPCs are made with one default NACL which have a default allow.
- custom NACLs are initially made with a default deny, but they're not automatically associated with any subnet. 
- Each VPC can only have 1 NACL.
- 1 NACL can be associated with multiple VPCs. 

## Security Groups (SG)
- SGs are attached to elastic network interfaces (ENIs).
- SGs are Stateful, meaning allowing or denying a request has the same effect on the response. 
- <ins>They have no explicit denies</ins> - they cannot block specific bad actors because we can't block specific IP or IP ranges. 
- Used in conjunction with NACLs which add explicit denies. 
- They support referencing AWS logical resources including other SGs and itself:
    <p>We can write a SG such that it allows outcoming requests from one subnet that come to another. This way, we can connect a private "app" subnet to a public "web" subnet, which in turn could accept incoming requests from anywhere in the web (0.0.0.0). Thus, we don't have to worry about IP addresses and CIDR ranges within a VPC. This is shown in the image below.</p>
    
    ![alt text](<Screenshots/Screenshot 2024-06-03 at 5.31.53 PM.png>)

    <p>The inbound rule on the right allows the web application to get requests from the public internet, and the inbound rule on the left lists the SG that allowed this public access as its source, thereby allowing access on port 1337 by it (so web can access app).</p>

    <p> When a SG's rule references itself and allows all communication, different resources all having that SG can communicate with one another </p>
