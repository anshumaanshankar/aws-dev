# TOPICS COVERED:
- EC2 Purchase Options
- Reserved instnaces
- Instance status checks, auto recovery
- Horizontal and Vertical scaling
- Instance metadata

## EC2 Purchase Options
`NOTE`: 1, 2, and 3 are more important for the exam. 
1. On demand (defualt): 
    - Different customers launch private, secure instances on shared hosts. There is no priority access given to the user.
    - Per second billing for consumed resources. Some resources are consumed when the instance is stopped too.
    - `use case`: Best for short term / unknown workloads which don't tolerate interruptions.
2. Spot (Cheapest):
    - Each EC2 host can hold upto 6 instances. 
    - When some space is left on a host unused by instances, they're offered at discounted rates (upto 90%) called spot price. Price depends on the spare capacity at that time, instance type. 
    - A customer can set the max. they want to pay for spot pricing. If the price exceeds that rate, instances that are running for the lower price are terminated. `UNRELIABLE`.
    - `use case`: Never use for services that cannot tolerate interruptions (mail servers, domain servers etc). Ok for services that can tolerate interruptions. Not good for long term usage. 
3. Reserved:
    - A committment to AWS that resources will be used for a long term (1 year / 3 years), in return for a lower rate.
    - Reduced / removed per second cost, as long as the reservation matches that instance.
    - Unused instances are still billed.
    - Reservations can be locked to a region, or to an AZ.
    - Payment structures: 
        - no payment upfront: pay per second (smallest discounts).
        - all upfront: no per second free (largest discounts).
        - partial upfront: lower per second cost.
    - `Use case`: For long term, consistent usage of EC2.
4. Dedicated Host:
    - You pay for a host, instances are not charged regardless of size, upto the host's capacity.
    - Host affinity: keeps instances connected to the same hosts.
    - `Use Case`: Socket and core licensing requirements.
5. Dedicated Instance:
    - Instances run on an EC2 host with other instances of yours alone. Host is not paid for, but is only allocated to you.
    - Extra fees: One-off hourly fee for each region where dedicated instances are used.
    - `Use Case`: When you need your own underlying hardware, but don't want to deal with a host.

## Reserved Instances
- Standard reserved: If you want long term, uninterrupted instances running for 1 or 3 years
- Scheduled reserved: 
    - If you have long term requirements that does not have to run uninterrupted. 
    - Slightly cheaper, but only usable in a pre-specified time period.
    - At least 1200 hours per year, at least one year. 
    - Is not supported in all regions.

### Capacity reservations
1. Regional reservation
    - valid instances can be launched in any AZ
    - Billing discount is given, 1 or 3 year commitment
    - No capacity is reserved in an AZ, which is risky when capacity can be limited
2. Zonal reservation
    - Same billing discount as regional, 1 or 3 year commitment
    - Reserve capacity in AZ
    - Instances launched in a diff AZ does not get the perks of zonal reservation
3. On demand capacity reservation
    - Constantly booked capacity inside an AZ, billed regardless of whether we consume it
    - No discount

### EC2 savings plan - An hourly commitment for 1 or 3 years 
1. Reservation of general compute $ amount (eg: $20 per hour, 3 years)
2. EC2 savings plan - flexibility on size and OS. 
3. Compute products are EC2, Lambda and Fargate. 

## Instance status checks, auto recovery
- There are 2 status checks for all instnaces. Each represents a set of tests:
    1. System status - Check impacting the EC2 service and host. Failure could indicate loss of system power, issues with EC2 host, loss of network connectivity.
    2. Instance status - Check impacting the EC2 instance. Failure could indicate corrupt file system, bad networking on the instance, OS kernal issues on the instance. 
- Handling the issues can be 
    1. Manual (stop & start, delete and recreate an instance)
    2. Ask EC2 to perform auto recovery. 

## Horizontal and Vertical scaling
### Vertical scaling
- For the case of EC2 instances, vertical scaling would mean resizing instances to allocate more resources.
- Resizes requires a reboot, meaning disruption. 
- There will always be a cap on vertical scaling, as it involves scaling individual instances. There will always be a largest size.

### Horizontal Scaling
- Add more instances, rather than increasing the size on an instance. 
- We could have multiple running copies of an application, in multiple instances. This would involve load balancing. 
- There is `no disruption` when scaling when sessions are externally hosted (off-host sessions).
- No real limits to scaling, more granularity (smaller increment %ages).
- Less expensive. 
![alt text](<Screenshots/Screenshot 2024-06-10 at 8.44.52 PM.png>)

## Instance metadata
- EC2 service provides data that is accessible inside all instances.
- IP address to access instance metadata is `169.254.169.254/latest/meta-data/`.
- Metadata is not encrypted, requires no permissions to access it (barring local firewall rules that you manually add).
- Metadata allows querying to get back information about an instance. 
- Metadata is used for networking, authentication, granting user data access. 
