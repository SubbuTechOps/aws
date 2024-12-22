# Steps for Creating IAM Users and Groups in AWS

## Step 1: Log in to AWS Management Console

1. Open the AWS Management Console.
2. Navigate to the **IAM (Identity and Access Management)** service.

## Step 2: Create an IAM Group

1. In the left navigation pane, select **Groups**.
2. Click on **Create group**.
3. Enter a name for the group: `admin`.
4. In the **Permissions** section, search for and select the **AdministratorAccess** policy.
5. Click **Create group** to finalize the creation.

## Step 3: Create an IAM User

1. In the left navigation pane, select **Users**.
2. Click on **Add user**.
3. Enter a username: `AdminUser1`.
4. Under **AWS credential type**, select **Password - AWS Management Console access**.
5. Provide a custom password and choose whether the user must reset the password on the first login.
6. Click **Next: Permissions**.

## Step 4: Add User to the Admin Group

1. Select **Add user to group**.
2. Select the group `admin`.
3. Click **Next: Tags** and optionally add tags for the user (e.g., `Key: Role, Value: Admin`).
4. Click **Next: Review** and ensure all details are correct.
5. Click **Create user** to finalize.

## Step 5: Add an Alias for User Login

1. In the IAM dashboard, select **Dashboard** in the left navigation pane.
2. Locate the **Account Alias** section.
3. Click **Create account alias** (or **Edit** if an alias already exists).
4. Enter an alias for the account (e.g., `admin-login-alias`).
5. Click **Save changes**.

## Step 6: Provide User Login Information

1. After user creation, download the `.csv` file with login details or copy the sign-in URL.
2. Share the following information securely with the user:
   - **Sign-in URL**: `https://admin-login-alias.signin.aws.amazon.com/console`
   - **Username**: `AdminUser1`
   - **Password**: As set during user creation.

## Step 7: Verify User Access

1. Log in with the provided credentials to ensure the user has administrative permissions.
2. Verify the user can access and manage AWS services as intended.

## Text-Based Structural Diagram

```
+----------------------+       +-----------------------+
|  IAM User Creation  |       |     IAM Group        |
|----------------------|       |-----------------------|
|  User: AdminUser1   |       |  Group: admin         |
|  Password: Custom   |       |  Permissions:         |
|  AWS Management     |       |  - AdministratorAccess|
|  Console Access     |       +-----------------------+
+----------------------+                |
             |                          |
             |                          |
             |                          V
             |                 +-----------------------+
             |                 | Add User to Group    |
             |                 |-----------------------|
             |                 |  User: AdminUser1    |
             |                 |  Group: admin        |
             |                 +-----------------------+
             |
             V
+----------------------+       +-----------------------+
| Account Alias        |       | Verify Access         |
|----------------------|       |-----------------------|
| Alias: admin-login-  |       |  User: AdminUser1     |
| alias                |       |  Permissions Verified|
+----------------------+       +-----------------------+
```

## Steps for Creating IAM Users and Groups in AWS Using Terraform

### Step 1: Write the Terraform Configuration

Create a file named `iam_user.tf` with the following content:

```hcl
provider "aws" {
  region = "us-east-1"
}

# Create IAM Group
resource "aws_iam_group" "admin_group" {
  name = "admin"
}

# Attach Administrator Access Policy to Group
resource "aws_iam_group_policy_attachment" "admin_policy" {
  group      = aws_iam_group.admin_group.name
  policy_arn = "arn:aws:iam::aws:policy/AdministratorAccess"
}

# Create IAM User
resource "aws_iam_user" "admin_user" {
  name = "AdminUser1"
  force_destroy = true
}

# Add User to Group
resource "aws_iam_user_group_membership" "admin_user_membership" {
  user = aws_iam_user.admin_user.name
  groups = [aws_iam_group.admin_group.name]
}

# Create Login Profile for User
resource "aws_iam_user_login_profile" "admin_user_login_profile" {
  user = aws_iam_user.admin_user.name
  password_reset_required = true
  pgp_key = "keybase:your_keybase_username"
}

# Set Account Alias
resource "aws_iam_account_alias" "alias" {
  account_alias = "admin-login-alias"
}
```

### Step 2: How to Get a Keybase PGP Key

1. Install the Keybase application from [Keybase Downloads](https://keybase.io/download).
2. Create a Keybase account if you do not have one.
3. Open the Keybase application or use the command-line interface.
4. Generate a PGP key by running:
   ```bash
   keybase pgp gen
   ```
5. Export your PGP public key with the command:
   ```bash
   keybase pgp export
   ```
6. Copy the exported key and use it in the `pgp_key` field in your Terraform configuration, formatted as:
   ```
   keybase:your_keybase_username
   ```

### Step 3: Initialize and Apply the Configuration

1. Initialize Terraform:
   ```bash
   terraform init
   ```
2. Validate the configuration:
   ```bash
   terraform validate
   ```
3. Apply the configuration:
   ```bash
   terraform apply
   ```
   Review the changes and confirm with `yes`.

### Step 4: Retrieve and Decrypt the Password

1. After running `terraform apply`, Terraform outputs an encrypted password for the user.
2. Copy the encrypted password.
3. Decrypt the password using Keybase:
   ```bash
   echo "ENCRYPTED_PASSWORD_HERE" | keybase pgp decrypt
   ```
   Replace `ENCRYPTED_PASSWORD_HERE` with the actual encrypted password from Terraform output.

### Step 5: Login to AWS Management Console

1. Share the following information securely with the user:
   - **Sign-in URL**: `https://${aws_iam_account_alias.alias.account_alias}.signin.aws.amazon.com/console`
   - **Username**: `AdminUser1`
   - **Password**: Use the decrypted password from Step 4.
2. The user logs in and sets a new password during the initial sign-in process.

### Step 6: Verify the Resources

1. Log in to the AWS Management Console.
2. Navigate to the IAM service to verify that the user, group, and account alias have been created.
3. Ensure the user `AdminUser1` has been added to the `admin` group with administrative permissions.

### Notes

- Replace `keybase:your_keybase_username` in the `aws_iam_user_login_profile` resource with your Keybase username or a valid PGP key.
- Ensure your AWS credentials are correctly configured before running Terraform commands.

### Output the Sign-in URL

Optionally, add an output block to display the sign-in URL:

```hcl
output "aws_signin_url" {
  value = "https://${aws_iam_account_alias.alias.account_alias}.signin.aws.amazon.com/console"
}
```

Run `terraform output` to view the URL after applying the configuration.

## Steps for Creating IAM Users and Groups in AWS Using CloudFormation

### Step 1: Write the CloudFormation Template

Create a file named `iam_user.yaml` with the following content:

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: Create an IAM User with Admin Group and Account Alias

Resources:
  AdminGroup:
    Type: AWS::IAM::Group
    Properties:
      GroupName: admin
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AdministratorAccess

  AdminUser1:
    Type: AWS::IAM::User
    Properties:
      UserName: AdminUser1

  AdminUser1ToGroup:
    Type: AWS::IAM::UserToGroupAddition
    Properties:
      GroupName: !Ref AdminGroup
      Users:
        - !Ref AdminUser1

  AdminUser1LoginProfile:
    Type: AWS::IAM::LoginProfile
    Properties:
      UserName: !Ref AdminUser1
      Password: "TemporaryPassword123!"
      PasswordResetRequired: true

  AccountAlias:
    Type: AWS::IAM::AccountAlias
    Properties:
      AccountAlias: admin-login-alias

Outputs:
  SignInURL:
    Value: !Sub "https://${AccountAlias}.signin.aws.amazon.com/console"
    Description: Sign-in URL for the AWS Management Console
```

### Step 2: Deploy the CloudFormation Template

1. Open the AWS Management Console.
2. Navigate to the **CloudFormation** service.
3. Click on **Create stack** > **With new resources (standard)**.
4. Upload the `iam_user.yaml` file or paste its contents into the editor.
5. Click **Next** and provide a stack name (e.g., `IAMUserStack`).
6. Click through the wizard and acknowledge the IAM permissions before clicking **Create stack**.

### Step 3: Retrieve Login Information

1. Once the stack creation is complete, navigate to the **Outputs** tab of the stack.
2. Copy the Sign-in URL provided in the outputs.
3. Share the following information securely with the user:
   - **Sign-in URL**: As shown in the Outputs.
   - **Username**: `AdminUser1`
   - **Password**: `TemporaryPassword123!`

### Step 4: Login and Verify Access

1. The user logs in using the provided credentials and resets the password during the first login.
2. Verify that the user has administrative access by accessing AWS services.

### Required IAM Permissions for CloudFormation Stack Execution

To execute the CloudFormation stack for this process, the following IAM permissions are required:

#### Permissions for IAM Resources:

- `iam:CreateUser`
- `iam:CreateGroup`
- `iam:AddUserToGroup`
- `iam:AttachGroupPolicy`
- `iam:CreateLoginProfile`
- `iam:CreateAccountAlias`
- `iam:UpdateAccountAlias`
- `iam:DeleteUser` (for rollback scenarios)
- `iam:DeleteGroup` (for rollback scenarios)
- `iam:RemoveUserFromGroup` (for rollback scenarios)
- `iam:DetachGroupPolicy` (for rollback scenarios)
- `iam:DeleteAccountAlias` (for rollback scenarios)

#### Permissions for CloudFormation Execution:

- `cloudformation:CreateStack`
- `cloudformation:UpdateStack`
- `cloudformation:DeleteStack`
- `cloudformation:DescribeStacks`
- `cloudformation:ListStackResources`

### Notes

- Replace the `TemporaryPassword123!` with a more secure password in the `AdminUser1LoginProfile` resource.
- Ensure the CloudFormation stack has the necessary permissions to create IAM resources.

