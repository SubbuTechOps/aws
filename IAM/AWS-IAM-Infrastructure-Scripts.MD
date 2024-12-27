# AWS IAM Infrastructure Scripts

## Directory Structure
```
aws-iam-infrastructure/
├── modules/
│   └── iam/
│       ├── variables.tf
│       ├── locals.tf
│       ├── users.tf
│       ├── groups.tf
│       ├── group_memberships.tf
│       ├── policies.tf
│       ├── roles.tf
│       └── outputs.tf
├── environments/
│   ├── prod/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── stage/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── dev/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── test/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── test_cases.tftest.hcl
└── versions.tf
```

## Installation & Usage

1. Clone this repository:
```bash
git clone [repository-url] aws-iam-infrastructure
cd aws-iam-infrastructure
```

2. Create PGP keys for password encryption:
```bash
# For each environment
gpg --gen-key
gpg --export [key-id] | base64 > environments/[env]/pubkey.gpg
```

3. Initialize Terraform:
```bash
terraform init
```

4. Run tests:
```bash
cd test
terraform init
terraform test
```

5. Deploy to an environment:
```bash
cd environments/[env]
terraform init
terraform plan
terraform apply
```

## File Contents

### versions.tf
```hcl
terraform {
  required_version = ">= 1.0.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

### modules/iam/variables.tf
```hcl
variable "environment" {
  description = "Environment name (prod, stage, dev)"
  type        = string
}

variable "common_tags" {
  description = "Common tags for all resources"
  type        = map(string)
  default     = {}
}

variable "pgp_key" {
  description = "PGP key for encrypting user passwords"
  type        = string
}

variable "global_admin_users" {
  description = "List of global admin users"
  type        = list(string)
  default     = []
}

variable "environment_admin_users" {
  description = "List of environment admin users"
  type        = list(string)
  default     = []
}

variable "service_team_users" {
  description = "Map of service teams to their users"
  type        = map(list(string))
  default     = {}
}

variable "application_team_users" {
  description = "Map of application teams to their users"
  type        = map(list(string))
  default     = {}
}

variable "automated_service_users" {
  description = "Map of automated service accounts"
  type        = map(list(string))
  default     = {}
}

variable "force_destroy" {
  description = "Force destroy users even if they have non-Terraform-managed IAM access keys"
  type        = bool
  default     = false
}
```

### modules/iam/locals.tf
```hcl
locals {
  # User Classifications
  user_types = {
    global_admin    = "GlobalAdmin"
    env_admin       = "EnvAdmin"
    service_team    = "ServiceTeam"
    app_team        = "AppTeam"
    service_account = "ServiceAccount"
  }

  # Resource Paths
  user_paths = {
    GlobalAdmin     = "/global-admins/"
    EnvAdmin        = "/env-admins/${var.environment}/"
    ServiceTeam     = "/service-teams/${var.environment}/"
    AppTeam         = "/app-teams/${var.environment}/"
    ServiceAccount  = "/service-accounts/${var.environment}/"
  }

  group_paths = {
    GlobalAdmin     = "/groups/global-admins/"
    EnvAdmin        = "/groups/env-admins/${var.environment}/"
    ServiceTeam     = "/groups/service-teams/${var.environment}/"
    AppTeam         = "/groups/app-teams/${var.environment}/"
    ServiceAccount  = "/groups/service-accounts/${var.environment}/"
  }

  # User Compilations
  all_users = merge(
    {
      for user in var.global_admin_users : user => {
        type        = local.user_types.global_admin
        environment = "all"
      }
    },
    {
      for user in var.environment_admin_users : user => {
        type        = local.user_types.env_admin
        environment = var.environment
      }
    },
    {
      for team, users in var.service_team_users : 
      for user in users : user => {
        type        = local.user_types.service_team
        environment = var.environment
        team        = team
      }
    },
    {
      for team, users in var.application_team_users :
      for user in users : user => {
        type        = local.user_types.app_team
        environment = var.environment
        team        = team
      }
    },
    {
      for team, users in var.automated_service_users :
      for user in users : user => {
        type        = local.user_types.service_account
        environment = var.environment
        team        = team
      }
    }
  )

  # Human vs Service Account Separation
  human_users = {
    for k, v in local.all_users : k => v
    if v.type != local.user_types.service_account
  }
}
```

### modules/iam/users.tf
```hcl
resource "aws_iam_user" "users" {
  for_each = local.all_users

  name          = each.key
  path          = lookup(local.user_paths, each.value.type, "/")
  force_destroy = var.force_destroy

  tags = merge(
    var.common_tags,
    {
      Name        = each.key
      Environment = each.value.environment
      UserType    = each.value.type
      ManagedBy   = "terraform"
    }
  )
}

resource "aws_iam_user_login_profile" "profiles" {
  for_each = local.human_users

  user                    = aws_iam_user.users[each.key].name
  password_reset_required = true
  password_length        = 20
  pgp_key               = var.pgp_key
}
```

### modules/iam/groups.tf
```hcl
# Global Admins Group
resource "aws_iam_group" "global_admins" {
  name = "Global-Admins"
  path = local.group_paths.GlobalAdmin
}

resource "aws_iam_group_policy_attachment" "global_admin_policy" {
  group      = aws_iam_group.global_admins.name
  policy_arn = "arn:aws:iam::aws:policy/AdministratorAccess"
}

# Environment Admins Group
resource "aws_iam_group" "env_admins" {
  name = "${title(var.environment)}-Admins"
  path = local.group_paths.EnvAdmin
}

resource "aws_iam_group_policy" "env_admin_policy" {
  name  = "${title(var.environment)}AdminPolicy"
  group = aws_iam_group.env_admins.name

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = "*"
        Resource = [
          "arn:aws:*:*:*:*/${var.environment}/*"
        ]
      }
    ]
  })
}

# Service Teams Groups
resource "aws_iam_group" "service_teams" {
  for_each = var.service_team_users

  name = "${each.key}-Admins"
  path = local.group_paths.ServiceTeam
}

# Application Teams Groups
resource "aws_iam_group" "app_teams" {
  for_each = var.application_team_users

  name = "App-${each.key}"
  path = local.group_paths.AppTeam
}

# Automated Services Groups
resource "aws_iam_group" "automated_services" {
  for_each = var.automated_service_users

  name = "${each.key}-Users"
  path = local.group_paths.ServiceAccount
}
```

### modules/iam/group_memberships.tf
```hcl
resource "aws_iam_user_group_membership" "global_admins" {
  for_each = toset(var.global_admin_users)

  user   = each.value
  groups = [aws_iam_group.global_admins.name]
}

resource "aws_iam_user_group_membership" "env_admins" {
  for_each = toset(var.environment_admin_users)

  user   = each.value
  groups = [aws_iam_group.env_admins.name]
}

resource "aws_iam_user_group_membership" "service_teams" {
  for_each = {
    for entry in flatten([
      for team, users in var.service_team_users : [
        for user in users : {
          team = team
          user = user
        }
      ]
    ]) : "${entry.team}-${entry.user}" => entry
  }

  user   = each.value.user
  groups = [aws_iam_group.service_teams[each.value.team].name]
}

resource "aws_iam_user_group_membership" "app_teams" {
  for_each = {
    for entry in flatten([
      for team, users in var.application_team_users : [
        for user in users : {
          team = team
          user = user
        }
      ]
    ]) : "${entry.team}-${entry.user}" => entry
  }

  user   = each.value.user
  groups = [aws_iam_group.app_teams[each.value.team].name]
}
```

### modules/iam/policies.tf
```hcl
resource "aws_iam_group_policy" "service_team_policies" {
  for_each = var.service_team_users

  name  = "${each.key}AdminPolicy"
  group = aws_iam_group.service_teams[each.key].name

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "${lower(each.key)}:*"
        ]
        Resource = "*"
      }
    ]
  })
}

resource "aws_iam_group_policy" "app_team_policies" {
  for_each = var.application_team_users

  name  = "App${each.key}Policy"
  group = aws_iam_group.app_teams[each.key].name

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "app:Read*",
          "app:List*",
          "app:Describe*",
          can(regex("Developers|PowerUsers", each.key)) ? "app:Write*" : "",
          each.key == "Developers" ? "app:Deploy*" : ""
        ]
        Resource = "*"
      }
    ]
  })
}
```

### modules/iam/outputs.tf
```hcl
output "user_arns" {
  description = "Map of usernames to their ARNs"
  value = {
    for user, details in aws_iam_user.users : user => details.arn
  }
}

output "group_arns" {
  description = "Map of group names to their ARNs"
  value = merge(
    {
      "global_admins" = aws_iam_group.global_admins.arn
      "env_admins"    = aws_iam_group.env_admins.arn
    },
    {
      for team, group in aws_iam_group.service_teams : "service_${team}" => group.arn
    },
    {
      for team, group in aws_iam_group.app_teams : "app_${team}" => group.arn
    }
  )
}

output "encrypted_passwords" {
  description = "Map of usernames to their encrypted passwords"
  value = {
    for user, profile in aws_iam_user_login_profile.profiles : user => profile.encrypted_password
  }
  sensitive = true
}
```

### environments/prod/main.tf
```hcl
provider "aws" {
  region = "us-west-2"
}

module "prod_iam" {
  source = "../../modules/iam"

  environment = "prod"
  
  pgp_key = file("${path.module}/prod-key.gpg")
  
  common_tags = {
    Environment = "prod"
    ManagedBy   = "terraform"
    CostCenter  = "production"
  }

  global_admin_users = [
    "prod-admin-1",
    "prod-admin-2"
  ]

  environment_admin_users = [
    "prod-env-admin-1",
    "prod-env-admin-2"
  ]

  service_team_users = {
    "Network" = [
      "prod-network-1",
      "prod-network-2"
    ]
    "Security" = [
      "prod-security-1",
      "prod-security-2"
    ]
    "Database" = [
      "prod-db-1",
      "prod-db-2"
    ]
  }

  application_team_users = {
    "Developers" = [
      "prod-dev-1",
      "prod-dev-2"
    ]
    "ReadOnly" = [
      "prod-ro-1",
      "prod-ro-2"
    ]
  }

  automated_service_users = {
    "CI-CD" = [
      "prod-jenkins",
      "prod-github-actions"
    ]
    "Monitoring" = [
      "prod-prometheus",
      "prod-grafana"
    ]
  }
}
```

### test/test_cases.tftest.hcl
```hcl
variables {
  environment = "test"
  pgp_key     = "test-key.gpg"
  common_tags = {
    Environment = "test"
    ManagedBy   = "terraform"
    Purpose     = "testing"
  }
  global_admin_users = ["test-admin"]
  environment_admin_users = ["test-env-admin"]
  service_team_users = {
    "Network" = ["test-network-admin"]
    "Security" = ["test-security-admin"]
    "Database" = ["test-db-admin"]
  }
  application_team_users = {
    "Developers" = ["test-dev-1", "test-dev-2"]
    "ReadOnly" = ["test-readonly-1"]
    "PowerUsers" = ["test-power-1"]
  }
  automated_service_users = {
    "CI-CD" = ["test-jenkins"]
    "Monitoring" = ["test-prometheus"]
  }
}

# Test User Creation
run "verify_user_creation" {
  command = plan

  assert {
    condition     = length(module.test_iam.user_arns) > 0
    error_message = "No users were created"
  }

  assert {
    condition     = contains(keys(module.test_iam.user_arns), "test-admin")
    error_message = "Global admin user was not created"
  }

  assert {
    condition     = contains(keys(module.test_iam.user_arns), "test-env-admin")
    error_message = "Environment admin user was not created"
  }
}

# Test Group Creation
run "verify_group_creation" {
  command = plan

  assert {
    condition     = length(module.test_iam.group_arns) > 0
    error_message = "No groups were created"
  }

  assert {
    condition     = contains(values(module.test_iam.group_arns), "arn:aws:iam::*:group/groups/global-admins/Global-Admins")
    error_message = "Global admin group was not created correctly"
  }
}

# Test Service Team Groups
run "verify_service_team_groups" {
  command = plan

  assert {
    condition = contains(keys(module.test_iam.group_arns), "service_Network")
    error_message = "Network admin group was not created"
  }

  assert {
    condition = contains(keys(module.test_iam.group_arns), "service_Security")
    error_message = "Security admin group was not created"
  }
}

# Test Application Team Groups
run "verify_app_team_groups" {
  command = plan

  assert {
    condition = contains(keys(module.test_iam.group_arns), "app_Developers")
    error_message = "Developers group was not created"
  }

  assert {
    condition = contains(keys(module.test_iam.group_arns), "app_ReadOnly")
    error_message = "ReadOnly group was not created"
  }
}

# Test Password Generation
run "verify_password_generation" {
  command = plan

  assert {
    condition     = length(module.test_iam.encrypted_passwords) > 0
    error_message = "No passwords were generated for human users"
  }

  assert {
    condition     = !contains(keys(module.test_iam.encrypted_passwords), "test-jenkins")
    error_message = "Password was incorrectly generated for service account"
  }
}

# Test Resource Paths
run "verify_resource_paths" {
  command = plan

  assert {
    condition = can(regex("^/global-admins/", data.aws_iam_user.test_admin.path))
    error_message = "Global admin user path is incorrect"
  }

  assert {
    condition = can(regex("^/groups/service-teams/test/", data.aws_iam_group.test_network.path))
    error_message = "Service team group path is incorrect"
  }
}

# Test Tags
run "verify_resource_tags" {
  command = plan

  assert {
    condition = all(
      aws_iam_user.users["test-admin"].tags["Environment"] == "test",
      aws_iam_user.users["test-admin"].tags["ManagedBy"] == "terraform"
    )
    error_message = "Required tags are missing or incorrect"
  }
}

# Test Policy Attachments
run "verify_policy_attachments" {
  command = plan

  assert {
    condition = contains(
      aws_iam_group_policy_attachment.global_admin_policy[*].policy_arn,
      "arn:aws:iam::aws:policy/AdministratorAccess"
    )
    error_message = "Administrator policy not attached to global admin group"
  }
}

# Test Service Account Configuration
run "verify_service_accounts" {
  command = plan

  assert {
    condition = !can(aws_iam_user_login_profile.profiles["test-jenkins"])
    error_message = "Service account incorrectly configured with login profile"
  }

  assert {
    condition = can(regex("^/service-accounts/test/", data.aws_iam_user.test_jenkins.path))
    error_message = "Service account path is incorrect"
  }
}

# Test Environment Separation
run "verify_environment_separation" {
  command = plan

  assert {
    condition = all(
      contains(split("/", data.aws_iam_group.test_env_admin.path), "test"),
      !contains(split("/", data.aws_iam_group.test_env_admin.path), "prod")
    )
    error_message = "Environment separation not properly enforced"
  }
}

# Test Group Membership
run "verify_group_membership" {
  command = plan

  assert {
    condition = contains(
      aws_iam_user_group_membership.global_admins["test-admin"].groups,
      aws_iam_group.global_admins.name
    )
    error_message = "Global admin not properly assigned to group"
  }
}

# Test Password Policy
run "verify_password_policy" {
  command = plan

  assert {
    condition = aws_iam_user_login_profile.profiles["test-admin"].password_reset_required
    error_message = "Password reset not required for new users"
  }

  assert {
    condition = aws_iam_user_login_profile.profiles["test-admin"].password_length >= 20
    error_message = "Password length requirement not met"
  }
}
```
