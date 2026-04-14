# AWS Identity and Access Management (IAM)

In this project, I set up two EC2 instances and managed access control using IAM policies 
and user groups. The goal was to deepen my understanding of Cloud Security on AWS.

## Project Walkthrough

<img width="770" height="595" alt="image" src="https://github.com/user-attachments/assets/d483fe24-6771-49bb-b998-7f4feddcde7b" />


1. Launch EC2 instances
   - Set up the first instance named prod-name, tagged with Key: Env, Value: production. 
     Selected Free Tier eligible settings and proceeded without a key pair.
   - Set up a second instance named dev-name with the same tag configuration.

2. Set up an IAM Policy

   The IAM policy was configured to grant access to the development instance only.
   - Navigated to IAM, created a new policy and used the contents of policy.json to 
     define dev environment permissions. Saved and named the policy.

3. Configure an Account Alias

   An account alias makes it easier for other users to sign in with a recognizable ID.
   - In the IAM dashboard, set up a custom account alias on the right-hand panel.

4. Set up IAM Users & User Groups
   - Created a user group in IAM and attached the previously created policy to it.
   - Created a new user and added them to the group.

5. Verify Access
   - Signed in using the IAM user login link.
   - Confirmed the correct region was selected matching where the instances were launched.
   - Attempted to stop both instances to observe the difference in permissions.

### Additional steps

1. IAM Policy Simulator
   - Used this tool to test and validate policies without making any changes to live resources.
