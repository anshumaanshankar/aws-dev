# TOPICS COVERED:
- Stateful v Stateless Firewalls
- Network Access Control lists (NACLs)
- Security Groups
- Network Address Translation (NAT) and NAT gateway

## Stateful v Stateless Firewalls
- When a connection is made from a computer to a website,
    1. A **request** is made from a ephemeral port on the user's laptop (random port no. b/w 1024 and 65535) and is sent to a destination port no. that's well known (for example, port 443 is https) along with the request for whatever data.
    2. A **response** is sent back by the server, having source address as that same 443 and the destination being the ephemeral port number.
- Requests can be both incoming or outgoing. **Whatever the direction of a request, the direction for the response is opposite.**

### Stateless Firewalls
- Stateless firewalls do not evaluate request and its reponse as a pair. 
- The request and response for the same connection will require separate rules, allowing or denying each individually. 
- On stateless firewalls, we need to allow the full range of ephemeral ports on outgoing responses.

### Stateful Firewalls
- Identifies request-response as pairs.
- Only one allow/deny is needed for a request, depending on which the response is also understood. 
- We don't need to allow the full ephemeral port range. 

## Network Access Control lists (NACLs)
- Every subnet has a NACL, which filters data as it crosses the boundary of the subnet: connections within a subnet are not affected.
- NACLs have a set of inbound rules and a set of outbound rules to explicitly allow/deny on requests and responses.(Inbound/outbound can request/response they are stateless).
- While communicating with services on different subnets, the NACL for each subnet will need needed permissions. 
- Rules match destination IP range, port number or protocols to decide an allow/deny. <ins>DENY-ALLOW-DENY applies.</ins>
- Rules are numbered and evaluated from low to high.
- If a rule number is "*", its the default rule that is applied when no other match is seen. Most often, its a deny. 
- VPCs are made with one default NACL which have a default allow.
- Custom NACLs are initially made with a default deny, but they're not automatically associated with any subnet. 
- Each VPC can only have 1 NACL, 1 NACL can be associated with multiple VPCs. 

## Security Groups (SG)
- SGs are attached to elastic network interfaces (ENIs).
- SGs are stateful, meaning allowing or denying a request has the same effect on the response. 
- <ins>They have no explicit denies</ins> - they cannot block specific bad actors because we can't block specific IP or IP ranges. 
- Used in conjunction with NACLs which add explicit denies. 
- They support referencing AWS logical resources including other SGs and itself:
    <p>We can write a SG such that it allows outcoming requests from one subnet that come to another. This way, we can connect a private "app" subnet to a public "web" subnet, which in turn could accept incoming requests from anywhere in the web (0.0.0.0). Thus, we don't have to worry about IP addresses and CIDR ranges within a VPC. This is shown in the image below.</p>
    
    ![alt text](<Screenshots/Screenshot 2024-06-03 at 5.31.53 PM.png>)

    <p>The inbound rule for "WEB" allows requests from the public internet (0.0.0.0) if they use TCP and port 443. The inbound rule for "APP" says that we allow communication coming from a source that uses this Security group, if the port number is 1337 and protocol used is TCP.
    </p>
    <p> When a SG's rule references itself and allows all communication, different resources all having that SG can communicate with one another.</p>

## Network Address Translation (NAT) and NAT gateway
- A set of processes that can change source and/or destination IPs.
- Internet Gateway (IGW) performs a <ins>static NAT</ins>: Changes private src IP to public on outgoing packets, and changes public dest. IP to private on incoming data packets.
- IP masquerading, often just called NAT, involves hiding an address range behind one IP. Used to give private address ranges to the outgoing internet access. NAT can't be used from public internet to access private IPs. This is done from NAT gateways.
- NAT is not needed for IPv6., NAT gateways don't work with IPv6. 
- Its meant for private IPv4 addresses to connect to the public internet. 

### Architecture:
- We have a private subnet with address range 10.16.32.0/20 inside which we have 3 instances, i-01 to i-03 with IP addresses 10.16.32.10/20/30
- All those 3 IPs are private.
- Using NAT, we can provision a NAT gateway into a public subnet. Using a routing table in the public subnet, we can send data out and get data back in with that NAT gateway being the source.
- Using a route table on the private subnet, we can create routes connecting i-01 to 03 to the NAT gateway in the public subnet. 

### Moving a data packet using NAT gateways
- A private instance transmits a packet that has a public internet destination IP.
- Using the routing table in the private subnet, the packet is sent to the NAT Gateway (NAT-G) which is present in the public subnet.
- The NAT-G makes note of where the packet came from and changes the src address from that of the instance to that of itself. 
- Using the routing table in this public subnet, this packet is sent to the IGW.
- The IGW knows that the packet came from the NAT gateway, modifies the source address from the NAT-G's private IP to its public IP.
- IGW sends the packet on the public internet to its destination.
- Thus, the NAT gateway takes data from various private IPs, shields them behind its own public IP and passes on those requests - IP masquerading!!
![alt text](<Screenshots/Screenshot 2024-06-03 at 6.34.46 PM.png>)

## Final structure of the VPC
![alt text](<Screenshots/Screenshot 2024-06-04 at 11.29.24 AM.png>)

- VPC is set up inside the region us-east-1 with address range 10.16.0.0/16
- A, B, C are the AZs of that region.
- In each AZ, we have 4 user-defined tiers (Reserved, DB, APP, WEB).
- Each tier in a region has 1 subnet. Thus, each AZ has 4 subnets.
- Blue subnets are private, Green are public. 
- If private instances want to communicate with the internet, we move data using the private subnet's routing table to the NAT gateway in green subnet. NAT changes source IP from the private instance's IP to its own.
- The NAT gateway points data to the IGW via the VPC router. IGW changes source IP from NAT's private to NAT's public.
- This way, private instances can "hide" behind a NAT gateway and communicate with the public internet. 