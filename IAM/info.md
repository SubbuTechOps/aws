### IAM: Users and Groups

AWS Identity and Access Management (IAM) is a global service that enables you to securely manage access to AWS services and resources. Here's a simple breakdown:

#### Key Features:

1. **Root Account**: This account is created by default when you set up AWS. It has full access to all AWS resources but should not be used for daily operations. Never share the root account credentials.

2. **Users**: These are individual identities created within your AWS account. Users represent people or applications that need access to AWS resources. Each user can have unique credentials, such as passwords and access keys.

3. **Groups**: Groups are collections of users. They help organize users and manage permissions easily by assigning policies to the group rather than individual users. Key points about groups:

   - Groups contain users but not other groups.
   - A user can belong to multiple groups or none at all.

#### Example:

- **Group: Developers**: Includes Subbu, NagRaju, and Niranjan.
- **Group: Audit Team**: Includes Niranjan.
- **Group: Operations**: Includes Sathya and AdiShankar.

Users like Niranjan can belong to multiple groups (e.g., Audit Team and Developers).

#### Text-Based Structural Diagram:
```
Root Account
  |
  |-- Group: Developers
  |      |-- Subbu
  |      |-- NagRaju
  |      |-- Niranjan
  |
  |-- Group: Audit Team
  |      |-- Niranjan
  |
  |-- Group: Operations
         |-- Sathya
         |-- AdiShankar
```

### IAM: Permissions
IAM uses JSON documents called **policies** to define permissions for users or groups. These policies specify which actions are allowed or denied on specific AWS resources.

#### Key Points:

- Policies are assigned to users or groups to control their access.
- Follow the **least privilege principle**: only give the permissions users need to perform their tasks.

#### Policy Example Breakdown:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:Describe*",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "elasticloadbalancing:Describe*",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "cloudwatch:ListMetrics",
        "cloudwatch:GetMetricStatistics",
        "cloudwatch:Describe*"
      ],
      "Resource": "*"
    }
  ]
}
```
- **Version**: Specifies the policy language version.
- **Statement**: Contains individual permissions.
  - **Effect**: "Allow" grants permissions; "Deny" restricts them.
  - **Action**: Lists the actions (e.g., "ec2:Describe*") the policy allows.
  - **Resource**: Specifies the resources these actions apply to ("*" means all resources).

#### Best Practices:

- Regularly review policies to ensure they follow the least privilege principle.
- Use IAM managed policies for common permissions.
- Test policies in a sandbox environment before applying them broadly.

IAM helps you define who can access what and under what conditions, ensuring your AWS environment remains secure.

#### Best Practices:

- **Least Privilege**: Grant only the permissions that are absolutely necessary.
- **Avoid Root Usage**: Use the root account only for specific administrative tasks.
- **Organize with Groups**: Assign permissions to groups instead of individual users for easier management.
- **MFA**: Enable Multi-Factor Authentication for added security.

IAM helps you define who can access what and under what conditions, ensuring your AWS environment remains secure.

