## TOPICS COVERED
- Permissions evaluation
- Cloud HSM

## Permissions Evaluation
### User tries to access resource in the same AWS account
- To determine if an IAM user can have access to a resource, all policies applicable to that user are gathered and evaluated in the following order:
    1. Check if there are any <ins>explicit denies</ins>. If any, game over.
    2. If no denies, check if any <ins>service control policies</ins> (Applied to AWS organizations, used to limit access to member accounts) exist. If they deny, game over. 
    3. If no SCP exists, or we have an explicit allow, we check <ins>resource policy</ins>. If the resource policy has an explicit allow, We have access. End of discussion.
    4. If We do not have explicit allow, check for <ins> permission boundaries</ins>. If there is one, is the action allowed? If not allowed, game over.
    5. If there is no boundary or there the action is allowed, check <ins>session policies</ins>. If we use an IAM role and has a session policy, check if we are allowed. If not, game over. 
    6. If we don't have a policy or we are allowed, move on to <ins>identity policies</ins>. If there is no explicit allow, game over. At this point, we need an explicit allow to have access. 
    ![alt text](<Screenshots/Screenshot 2024-05-31 at 2.00.07 PM.png>)

    ### User tries to access resource in different AWS accounts
    - When we account A where the user resides, and account B where the resource resides, we follow the steps given below:
        1. We check all permmissions for the user in account A (7 step process listed above)
        2. Account B must allow access from account A and account A must allow access to account B. If one or the other is not allowed, access is denied. 
        ![alt text](<Screenshots/Screenshot 2024-05-31 at 2.03.48 PM.png>)