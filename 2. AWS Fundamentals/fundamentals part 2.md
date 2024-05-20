## TOPICS COVERED
- CloudFormation

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

- The example template used for the demo is in the root folder labeled ec2instance.yaml