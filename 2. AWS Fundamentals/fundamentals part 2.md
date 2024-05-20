## TOPICS COVERED
- CloudFormation
- Cloudwatch

## CloudFormation
- Lets you create, update and delete infrastructure using <ins>templates</ins> on AWS.
- Templates can be reused to make/delete/updates resources much faster than when done manually.
- Templates are written in YAML or JSON.

- **Components of a template**:
    - Resources: that is what tells cloudformation what to do (Create/Update/Delete). This is the only mandatory part of the template.
    - Description: allows the author add a description about what the template does. If we have a <ins>template format version</ins> (another field), the description must follow it.
    - Metadata: controls the UI, among others.
    - Parameters: Fields that prompt the user for more info, we can add criteria and default values of those parameters too.
    - Mapping: allows lookup tables to be made. 
    - Conditions: Allows decision making in the template - certain things only occur when conditions are met. 
    - Outputs: once the template is made, we see outputs to show what is created / updated or deleted. For example, an instance ID of an EC2 instance that is made.

- The example template used for the demo is in the same folder labeled ec2instance.yaml

## Cloudwatch
- A product that collects and manages operational data.
- Main functions include:
    - Metrics collection via cloudwatch allows also for monitoring and actions based on data collected.
    - Cloudwatch logs allows collection, monitoring and actions based on log data.
    - Cloudwatch events that allows for event generation based on an occurring event (EC2 instance stopped for example), or a timed event (thing to do on a day of the week, for example).

![alt text](<Screenshot 2024-05-20 at 11.41.12 AM.png>)

The above image shows the architecture of cloudwatch basics. CPU, memory and disk utliziation are some metrics that we can collect. Based on these, we can perform actions.

- Namespace: a container that is used to organize data by metrics. All AWS data go into a namespace called AWS/servicename. We can use cloudwatch for our own data and create namespaces of our own. these namespaces will contain the related metrics 

- Metrics: a collection of related datapoints in a time ordered structure. For example, we could have a metric called CPU-utilization, that takes datapoints from EC2 instances to indicate what percentage of the CPU is being utlizied by each instance. Each instance would send in 1 datapoint at fixed intervals.

- Datapoint: contains a timestamp (down to the second) and a value (related to whatever metric the datapoint speaks about).

- Alarms: Created and configured to a metric. We can say that some things are not ok, which would trigger the alarm. Based on the trigger, an action is performed. Eg: the billing alarm we make. The third state of an alarm (ok/alarm) is insufficient data.

-