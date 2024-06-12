# TOPICS COVERED
- Cloudwatch architecture
- Cloudwatch logs architecture
- AWS X-Ray
- VPC Flow Logs

## Cloudwatch architecture
- <ins> Provides a platform for ingestion, storage and management of metrics. </ins>
- Some services have automatic metric ingestion to cloudwatch. This is call `AWS service integration`.
- Any metrics *inside* a service have to be uploaded to cloudwatch using the `cloudwatch agent`.
- Using the API or agent, we can include cloudwatch into our application, or enable on-premises integration. These are known as `custom metrics`.
![alt text](<Screenshots/Screenshot 2024-06-11 at 10.43.08 AM.png>)

- The architecture shows that on premise VMs and internet connected applications can directly store metrics on cloudwatch.
- Private services running in VPCs store metrics on cloudwatch by:
    1. Using an internet gateway (requires IPv4 public addressing).
    2. Connecting to an interface endpoint (does not require IPv4 public addressing).

### Terms
1. Namespace: container (folder) holding metrics. They're named as `AWS/service-name` for AWS services, custom ones are not named like so. They separate the metrics of diff services.
2. Datapoints: A timestamp and value (with an optional unit of measure).
3. Metric: A time ordered set of datapoints. Example: CPUUtilization is a metric that is associated with EC2. CPUUtilization is the metric name, and we can find it inside the AWS/EC2 namespace. 
4. Dimensions: A `name:value` parameter added to datapoints. They're used to differentiate b/w diff devices or instances of the same service Eg: If we have more than 1 EC2 instance running, We would not know which datapoint of CPUUtilization corresponds to which instance, without using dimensions.
5. Resolution: The time intervals at which datapoints are added to cloudwatch. By defualt, its `60secs`. We can use shorter intervals for extra cost (upto 1 second). 
6. Retention: How long a datapoint is held. It depends on the resolution.
    Resolution | Time Held
    -----------|-----------
    < 60 sec   |  3 hours
    60 sec     |  15 days
    300 sec    |  63 days
    3600 sec   |  455 days
7. Statistics: Aggregation of data collected over a period (min/max/sum/average)
8. Percentile: Relative standing of a value in a dataset - useful in understanding metric data distribution
9. Alarms: Monitor metrics over a given time period. Alarms can initiate actions when triggered. 

## Cloudwatch Logs architecture
- Its a public service that stores, monitors and provides access to logging data. 
- Provides a logging platform for AWS services, on-premise servers, IOT or other applications; VPC flow logs, Cloudtrail, lambda etc.

### Ingestion
- Cloudwatch agent allows system or custom application logging.
- Log event is a timestamp-rawmessage format sent by a source and stored in cloudwatch logs. 
- A given source's logs are collected into a logstream. 
- Log groups are used to combine log streams that hold similar information, but from diff sources. 
- Metric filter, Retenions, Permissions and encryptions are added at the log group level. 
- Metrics can be made from metric filters. 
- Logging data can be exported to S3. `Exporting cannot be done in real time. Takes up to 12 hours.`

### Subscription
- We can allow real-time delivery of logging data from CW logs to other systems in a log group level. 
- A subscription filter is used to do this. We can what data gets handled, set the destination ARN, control how the data is grouped as its sent out, Define permissions to allow access for CW logs to the destination. 
- Using subscription filters from different accounts / regions, we can aggregate logging data. 
- To achieve near real-time delivery using subscription filter, we can connect the filter to:
    1. Elasticsearch, via an AWS managed lambda function
    2. External apps, via a custom lambda function
    3. Deliver to a kenesis datastream
    4. use Kenesis data firehose for near real-time delivery to S3. 
- We can then send it to S3 using Kenesis data firehose.

## AWS X-Ray
![alt text](<Screenshots/Screenshot 2024-06-11 at 11.55.42 AM.png>)

## VPC Flow Logs

- Flow logs are virtual monitors applied at 
    1. The VPC level - all subnets
    2. Subnet level - all ENIs of that subnet
    3. ENI level
- they capture packet metadata (packet sniffers capture packet data). `Not real time`.
- The logs can be stored either on S3, or on Cloudwatch logs. 
- Flow logs stored in S3 can be queried using Athena in an SQL like format. We would only pay for the data read and that stored on S3.

### Working
- Imagine a VPC with one public subnet on which an app is running, and one private subnet where the app's database is. 
- As mentioned earlier, Flow logs can be caught at the VPC, subnet or ENI level. 
- Flow logs can capture metadata on all accepted/rejected/all actions.
- Each log has multiple fields of information, including but not limited to:
    - srcaddr: source IP
    - dstadder: Destination IP
    - srcport: Source port
    - dstport: Destination port
    - protocol: ICMP is 1, TCP is 6, UDP is 17
    - action: accepted/reject
