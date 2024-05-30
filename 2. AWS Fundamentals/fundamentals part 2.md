## TOPICS COVERED
- Cloudwatch
- Shared Responsibility Model
- High Availability, Fault Tolerance and Disaster Recovery
- Route 53
- DNS Record Types


## Cloudwatch
- A product that collects and manages operational data.
- Main functions include:
    - Metrics collection via cloudwatch allows also for monitoring and actions based on data collected.
    - Cloudwatch logs allows collection, monitoring and actions based on log data.
    - Cloudwatch events that allows for event generation based on an occurring event (EC2 instance stopped for example), or a timed event (thing to do on a day of the week, for example).

![alt text](<Screenshots/Screenshot 2024-05-20 at 11.41.12 AM.png>)

The above image shows the architecture of cloudwatch basics. CPU, memory and disk utliziation are some metrics that we can collect. Based on these, we can perform actions.

- Namespace: a container that is used to organize data by metrics. All AWS data go into a namespace called AWS/servicename. We can use cloudwatch for our own data and create namespaces of our own. these namespaces will contain the related metrics 

- Metrics: a collection of related datapoints in a time ordered structure. For example, we could have a metric called CPU-utilization, that takes datapoints from EC2 instances to indicate what percentage of the CPU is being utlizied by each instance. Each instance would send in 1 datapoint at fixed intervals.

- Datapoint: contains a timestamp (down to the second) and a value (related to whatever metric the datapoint speaks about).

- Alarms: Created and configured to a metric. We can say that some things are not ok, which would trigger the alarm. Based on the trigger, an action is performed. Eg: the billing alarm we make. The third state of an alarm (ok/alarm) is insufficient data.

## Shared Responsibility Model
![alt text](<Screenshots/Screenshot 2024-05-20 at 1.28.20 PM.png>)

The image above lists what part of the product AWS manage, and which elements you have to manage. 

## High Availability, Fault Tolerance and Disaster Recovery

**High Availability (HA)**: 
- Aims to ensure an agreed level of optimal performance, usually uptime, for higher than a normal period. 
- Its about maximizing a system's online time. 
- User disruption is ok, because HA is not about avoiding it.
- <ins>HA IS NOT ABOUT OPERATING THROUGH FAILURE</ins>

**Fault Tolerance (FT)**:
- The property of a system that enables it to operate properly in the event of failure of some of its components. 
- The system is designed to work through failure with no disruption. 
- Fault tolerance is one step above high availability. It accounts for high availability, but on top of that, it enables the system to work through the problem(s).
- Harder to design, implement and costs more than HA. 

**Disaster Recovery (DR)**:
- what to do when HA and FT fail.
- It takes into account what happens before (why the failure happens) and what to do after.
- Ideas of a backup / resilience that are ready for whenever needed.
- <ins>NEVER STORE THE BACKUP WITH THE MAIN SITE</ins>
- DR prevents loss of things that are irreplaceable.
- Cloud allows DR to be automated. 

![alt text](<Screenshots/Screenshot 2024-05-20 at 1.50.58 PM.png>)

## Route 53
- Allows you to register domains
- Creates and manages zone files in AWS: Hoseted on four managed name servers
- Hosted zones can be public or private 

## DNS Record Types

- Nameserver Records:
    - How delegation works in DNS
    - Handles A records (matches host to ipv4 address) and AAAA records (matches host to ipv6 address)
- CNAME Records:
    - Allows host to host records
    - Cnames point at other names (A or AAAA)
- MX Records:
    - How a server finds the mail server for a domain.
    - Assume we are in the google.com zone.
    - Two parts: priority and value.
    - The value could be just a value (for eg: MX 10 mail). This means that the mail is in the same zone we are in. If we have a dot on the right (mail.otherdomain) this means that we are using a full domain name. 
- TXT Records:
    - Used to prove domain ownership
    - Allow us to add text to a domain, to provide additional functionality. 

- TTL (Time to Live)
    - indicate how long records are cached for
    - set by a domain owner
    - Results of a query (reaching a server) is cached on the resolver server for that TTL. 
