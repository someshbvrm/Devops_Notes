
***

# 🏗️ Terraform Mastery: Meta-Arguments, Modules, Workspaces & Real-World Patterns

## 🔧 Core Terraform Meta-Arguments

Meta-arguments control *how* resources are created and managed. They are essential for writing dynamic, reusable infrastructure code.

### 1. `count` - For Multiple Identical Resources
Use `count` to create multiple instances of the same resource with identical configurations. Resources are indexed numerically (`[0]`, `[1]`, etc.).

**✅ Example: Identical EC2 Instances**
```hcl
variable "instance_count" {
  description = "Number of identical web servers"
  type        = number
  default     = 3
}

resource "aws_instance" "web_server" {
  count         = var.instance_count # Creates 3 instances
  ami           = "ami-0abcdef1234567890"
  instance_type = "t2.micro"

  tags = {
    Name = "web-server-${count.index + 1}" # web-server-1, web-server-2, web-server-3
  }
}
```
**📌 Real-World Use Case:** Scaling out identical web servers in an Auto Scaling Group (before the ASG itself is created), creating multiple similar subnets.

---

### 2. `for_each` - For Resources with Unique Configs
Use `for_each` to create multiple instances where each requires a unique configuration. It works with maps or sets and identifies instances by key, not index.

**✅ Example: EC2 Instances with Different Configs**
```hcl
variable "instances" {
  description = "Map of instance names to their types"
  type        = map(string)
  default = {
    "application" = "t3.medium"
    "cache"       = "t3.small"
    "batch"       = "t3.large"
  }
}

resource "aws_instance" "app_server" {
  for_each      = var.instances
  ami           = "ami-0abcdef1234567890"
  instance_type = each.value # The value from the map

  tags = {
    Name = each.key # The key from the map: "application", "cache", "batch"
  }
}
```
**📌 Real-World Use Case:** Creating S3 buckets with different names and properties, setting up IAM roles with unique policies, defining security groups with different rules.

---

### 3. `depends_on` - Explicit Dependency Management
Forces Terraform to recognize implicit dependencies it cannot automatically infer.

**✅ Example: Ensure S3 Bucket Exists Before EC2 Instance**
```hcl
resource "aws_s3_bucket" "data_lake" {
  bucket = "my-company-data-lake"
  acl    = "private"
}

resource "aws_instance" "data_processor" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t3.medium"

  # Explicitly depend on the S3 bucket
  depends_on = [aws_s3_bucket.data_lake]

  user_data = <<-EOF
              #!/bin/bash
              aws s3 ls s3://my-company-data-lake/ # This script needs the bucket to exist
              EOF
}
```
**📌 Real-World Use Case:** Ensuring a DNS record is created only after a Load Balancer is fully provisioned, or making sure an IAM role exists before an EC2 instance that uses it is launched.

---

### 4. `provider` - Multi-Cloud & Multi-Region
Configures the provider for a specific resource or module, enabling multi-region or multi-account deployments.

**✅ Example: Deploying Resources in Multiple AWS Regions**
```hcl
# Configure the default provider (us-east-1)
provider "aws" {
  region = "us-east-1"
  alias  = "us_east_1"
}

# Configure an additional provider for eu-west-1
provider "aws" {
  region = "eu-west-1"
  alias  = "eu_west_1"
}

# Create a resource in us-east-1 (using the default alias)
resource "aws_s3_bucket" "us_bucket" {
  provider = aws.us_east_1
  bucket   = "my-us-bucket"
}

# Create a resource in eu-west-1
resource "aws_s3_bucket" "eu_bucket" {
  provider = aws.eu_west_1
  bucket   = "my-eu-bucket"
}
```
**📌 Real-World Use Case:** Multi-region disaster recovery setups, deploying resources to different AWS accounts for governance (e.g., dev vs. prod accounts).

---

### 5. `lifecycle` - Control Resource Lifecycle
Fine-tunes how resources are created, updated, and destroyed.

**✅ Example: Zero-Downtime Updates & Prevention of Accidental Deletion**
```hcl
resource "aws_instance" "critical_app" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t3.medium"

  lifecycle {
    create_before_destroy = true  # 🟢 Avoid downtime: create new before destroying old
    prevent_destroy       = true  # 🔒 Safety: block `terraform destroy` on this resource
    ignore_changes = [            # ⚙️ Flexibility: ignore manual tag changes
      tags["LastUpdated"],
      ami # Ignore AMI changes to prevent unintended recreations
    ]
  }
}
```
**📌 Real-World Use Case:**
- `create_before_destroy`: Essential for Load Balancers, NAT Gateways, and other resources where downtime is unacceptable.
- `prevent_destroy`: Protecting production databases (RDS) or critical S3 buckets containing logs.
- `ignore_changes`: Allowing auto-scaling policies to adjust the `desired_capacity` without Terraform overriding it.

---

## 🧱 Terraform Modules: The Building Blocks

Modules are containers for multiple resources that are used together. They are the primary way to package and reuse resource configurations.

### 🎯 Why Use Modules?
- **Reusability:** Write once, use everywhere (across environments and projects).
- **Abstraction:** Hide complexity behind a simple interface (input variables).
- **Standardization:** Enforce organizational best practices and security compliance.
- **Maintainability:** Update a module in one place to propagate changes to all who use it.
- **Collaboration:** Teams can work on different modules independently.

### 📁 Standard Module Structure
A well-structured module is self-contained and has a clear purpose.
```
modules/eks-cluster/
├── main.tf           # Primary resources for the EKS cluster
├── variables.tf      # Input variables (e.g., cluster name, node type)
├── outputs.tf        # Output values (e.g., cluster endpoint, kubeconfig)
└── README.md         # Documentation on how to use the module
```

### ✅ Example: A Reusable VPC Module
**`modules/vpc/variables.tf`**
```hcl
variable "name" {
  description = "Name of the VPC"
  type        = string
}

variable "cidr_block" {
  description = "CIDR block for the VPC"
  type        = string
  default     = "10.0.0.0/16"
}

variable "tags" {
  description = "Common tags to apply to all resources"
  type        = map(string)
  default     = {}
}
```
**`modules/vpc/main.tf`**
```hcl
resource "aws_vpc" "this" {
  cidr_block           = var.cidr_block
  enable_dns_hostnames = true

  tags = merge(
    var.tags,
    {
      Name = var.name
    }
  )
}

# ... (subnets, route tables, internet gateway, etc.)
```
**`modules/vpc/outputs.tf`**
```hcl
output "vpc_id" {
  description = "ID of the created VPC"
  value       = aws_vpc.this.id
}

output "private_subnet_ids" {
  description = "IDs of the private subnets"
  value       = aws_subnet.private[*].id
}
```
**`live/production/main.tf` (Using the Module)**
```hcl
module "prod_vpc" {
  source = "../../modules/vpc" # Path to the module

  name       = "production-vpc"
  cidr_block = "10.1.0.0/16"

  tags = {
    Environment = "production"
    ManagedBy   = "Terraform"
  }
}

# Reference the module's outputs
resource "aws_instance" "app" {
  # ... other config ...
  subnet_id = module.prod_vpc.private_subnet_ids[0] # Use an output from the module
}
```

---

## 🧰 Terraform Workspaces: Managing Environments

Workspaces allow you to manage multiple distinct sets of infrastructure resources (environments) with the same configuration.

### 🔧 Basic Workspace Commands
```bash
# List all workspaces
terraform workspace list

# Show the current workspace
terraform workspace show

# Create a new workspace (e.g., for a new environment)
terraform workspace new staging

# Select an existing workspace
terraform workspace select production

# Delete a workspace (caution!)
terraform workspace delete development
```

### 🎯 Using Workspaces in Configuration
Use `terraform.workspace` to dynamically change values based on the environment.

**✅ Example: Environment-Specific Configuration**
```hcl
# Define settings for each environment using a local map
locals {
  environment_settings = {
    dev = {
      instance_type = "t3.micro"
      instance_count = 1
      cidr_block = "10.0.0.0/16"
    }
    staging = {
      instance_type = "t3.small"
      instance_count = 2
      cidr_block = "10.1.0.0/16"
    }
    production = {
      instance_type = "t3.medium"
      instance_count = 3
      cidr_block = "10.2.0.0/16"
    }
  }

  # Select the settings for the current workspace
  env = local.environment_settings[terraform.workspace]
}

# Use the selected settings
resource "aws_instance" "example" {
  count         = local.env.instance_count
  ami           = "ami-0abcdef1234567890"
  instance_type = local.env.instance_type

  tags = {
    Environment = terraform.workspace
    Name        = "server-${terraform.workspace}-${count.index}"
  }
}

resource "aws_vpc" "example" {
  cidr_block = local.env.cidr_block
  # ... other configuration ...
}
```

### ⚠️ Workspace Limitations & Best Practices
- **State Isolation:** Each workspace uses the same backend but stores its state in a different key. This is not as strong as fully separate state files.
- **Not for Strong Isolation:** For completely isolated environments (e.g., different AWS accounts), use separate Terraform configurations and root modules instead of relying solely on workspaces.
- **Use with Modules:** Workspaces are most powerful when combined with modules, allowing you to reuse the same code for `dev`, `staging`, and `prod`.

---

## 🚀 Putting It All Together: A Real-World Scenario

**Goal:** Deploy a web application with a VPC, EC2 instances, and an S3 bucket across multiple environments using modules and workspaces.

**Project Structure:**
```
my-infrastructure/
├── modules/
│   ├── vpc/
│   ├── web-app/
│   └── s3-bucket/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   └── terraform.tfvars
│   ├── staging/
│   └── production/
└── scripts/
    └── deploy.sh
```

**`environments/dev/main.tf`**
```hcl
terraform {
  required_version = ">= 1.0"
  backend "s3" {
    bucket         = "my-company-tfstate"
    key            = "environments/dev/terraform.tfstate" # Unique key per env
    region         = "us-east-1"
    dynamodb_table = "terraform-state-lock"
    encrypt        = true
  }
}

provider "aws" {
  region = "us-east-1"
}

# Get the environment from the workspace
locals {
  env_name = terraform.workspace
}

# Deploy the networking foundation
module "networking" {
  source = "../../modules/vpc"

  name       = "web-app-${local.env_name}"
  cidr_block = "10.0.0.0/16"
}

# Deploy the application itself
module "web_application" {
  source = "../../modules/web-app"

  name          = "web-app-${local.env_name}"
  vpc_id        = module.networking.vpc_id
  subnet_ids    = module.networking.public_subnet_ids
  instance_type = "t3.micro"
  instance_count = 2
}

# Deploy a bucket for uploads
module "uploads_bucket" {
  source = "../../modules/s3-bucket"

  bucket_name = "web-app-uploads-${local.env_name}"
  is_public   = false # Dev bucket is private
}
```

**Deploying to an Environment:**
```bash
cd environments/dev/
terraform init
terraform workspace new dev # If it doesn't exist
terraform workspace select dev
terraform plan -var-file="terraform.tfvars"
terraform apply -var-file="terraform.tfvars" -auto-approve
```

This structure provides a scalable, maintainable, and collaborative foundation for managing infrastructure of any size.