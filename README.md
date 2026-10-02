# terraform_learning
------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Terraform — Complete A-to-Z

## 1. What is Terraform?

Terraform is an **Infrastructure as Code (IaC)** tool used to create, configure, modify, and delete infrastructure using configuration files instead of manually creating resources through cloud-provider consoles. Terraform allows you to describe what infrastructure you want, and Terraform determines what actions are required to make the real infrastructure match that desired configuration. You can use Terraform with AWS, Azure, Google Cloud, Kubernetes, databases, networking systems, and many other platforms.

For example, instead of manually going into AWS Console and creating an EC2 instance, security group, VPC, and S3 bucket, you can describe them in Terraform files:

```text
Terraform Code
      ↓
Terraform
      ↓
AWS API
      ↓
AWS Resources
```

---

# 2. Why do we need Terraform?

Imagine that your company needs the same infrastructure for Development, Testing, Staging, and Production. Creating everything manually through a cloud console can take a lot of time and can introduce human errors. Terraform lets you define infrastructure as code so that the same configuration can be reused, reviewed, version-controlled, and recreated when needed. If a server is accidentally deleted, Terraform can determine what is missing and create it again according to the configuration.

Without Terraform:

```text
Engineer
   ↓
AWS Console
   ↓
Click many options
   ↓
Create resources manually
```

With Terraform:

```text
Terraform Code
      ↓
terraform plan
      ↓
Review changes
      ↓
terraform apply
      ↓
Infrastructure created
```

---

# 3. What is Infrastructure as Code?

**Infrastructure as Code**, or IaC, means managing infrastructure through machine-readable configuration files instead of manually configuring infrastructure through graphical interfaces. The infrastructure definition becomes part of your source code and can therefore be stored in Git, reviewed through pull requests, tested, and reused.

For example, instead of saying:

> Go to AWS → EC2 → Launch Instance → Choose Ubuntu → Choose instance size → Configure networking...

you write:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"
}
```

Terraform interprets this configuration and communicates with AWS.

---

# 4. Declarative vs Imperative

Terraform is primarily **declarative**. In a declarative approach, you describe the desired final state rather than writing every individual step needed to reach that state. Terraform compares your desired configuration with the current infrastructure and determines what changes are necessary.

For example, you tell Terraform:

```text
I want:
1 EC2 instance
1 Security Group
1 S3 bucket
```

You don't normally tell Terraform:

```text
Step 1 → Create security group
Step 2 → Wait
Step 3 → Create instance
Step 4 → Attach security group
...
```

Terraform determines the required actions.

Remember:

```text
Imperative → HOW to do it

Declarative → WHAT you want
```

---

# 5. Terraform Architecture

The basic Terraform workflow is:

```text
                Terraform Code
                      ↓
                terraform init
                      ↓
                terraform plan
                      ↓
                terraform apply
                      ↓
                Provider
                      ↓
                Cloud API
                      ↓
              Infrastructure
```

For AWS:

```text
Terraform
    ↓
AWS Provider
    ↓
AWS APIs
    ↓
EC2 / VPC / S3 / IAM / RDS
```

---

# 6. What is Terraform Configuration?

Terraform configuration is the collection of `.tf` files that describe your infrastructure. Terraform configuration is written in **HCL**, which stands for HashiCorp Configuration Language. You can split your configuration into multiple files, such as `main.tf`, `variables.tf`, `outputs.tf`, and `providers.tf`.

A simple project may look like:

```text
terraform-project/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
└── providers.tf
```

---

# 7. What is HCL?

**HCL**, or HashiCorp Configuration Language, is the configuration language commonly used by Terraform. It is designed to be human-readable while still being structured enough for Terraform to process.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"
}
```

You can understand this as:

```text
resource
   ↓
AWS EC2 instance
   ↓
name = web
   ↓
instance type = t3.micro
```

---

# 8. What is a Provider?

A **provider** is a Terraform plugin that allows Terraform to communicate with an external platform or API. Terraform itself does not know how to create an AWS EC2 instance or an Azure VM. The AWS provider understands how to communicate with AWS APIs, while the Azure provider understands Azure APIs.

Examples:

```text
AWS Provider
Azure Provider
Google Cloud Provider
Kubernetes Provider
GitHub Provider
```

For AWS:

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

This tells Terraform to use the AWS provider and work in the specified AWS region.

---

# 9. What is `terraform init`?

`terraform init` is usually the **first Terraform command** you run inside a new Terraform project. It initializes the working directory, downloads required providers, initializes modules when applicable, and prepares Terraform's working files.

Command:

```bash
terraform init
```

You can think of it as:

```text
Terraform project
      ↓
terraform init
      ↓
Download/setup dependencies
      ↓
Ready to work
```

If you're using AWS:

```bash
terraform init
```

will download the required AWS provider based on your configuration.

---

# 10. What is `terraform validate`?

`terraform validate` checks whether your Terraform configuration is syntactically and structurally valid.

Command:

```bash
terraform validate
```

Example:

```text
Success! The configuration is valid.
```

This does **not** mean your infrastructure will definitely work. It primarily checks the configuration structure and internal consistency that Terraform can validate.

A useful workflow is:

```text
terraform fmt
       ↓
terraform validate
       ↓
terraform plan
```

---

# 11. What is `terraform fmt`?

`terraform fmt` formats Terraform configuration files according to Terraform's standard formatting conventions.

Command:

```bash
terraform fmt
```

You can format all files recursively:

```bash
terraform fmt -recursive
```

Formatting is important because consistent code is easier for teams to read and review.

---

# 12. What is a Resource?

A **resource** represents an infrastructure object that Terraform manages. Examples include EC2 instances, S3 buckets, VPCs, security groups, subnets, databases, and Kubernetes resources.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"
}
```

Here:

```text
aws_instance → resource type
web          → resource name
```

Terraform internally identifies this resource as:

```text
aws_instance.web
```

---

# 13. Resource Type and Resource Name

This is an important Terraform concept.

In:

```hcl
resource "aws_instance" "web" {
  ...
}
```

the first value:

```text
aws_instance
```

is the **resource type**.

The second:

```text
web
```

is the **resource name**.

Together:

```text
aws_instance.web
```

identify the Terraform resource within the configuration.

---

# 14. What is `terraform plan`?

`terraform plan` is one of the most important Terraform commands. It compares your desired configuration with the current state known to Terraform and determines what changes would be required. It shows you whether Terraform plans to create, modify, or destroy resources.

Command:

```bash
terraform plan
```

You might see:

```text
+ create
~ update
- destroy
-/+ replace
```

A useful way to remember:

```text
plan = "What will Terraform do?"
```

It is a preview and does not normally apply the changes.

---

# 15. What is `terraform apply`?

`terraform apply` executes the changes proposed by Terraform and attempts to make the actual infrastructure match your configuration.

Command:

```bash
terraform apply
```

Terraform normally shows a plan and asks for confirmation.

You can also save a plan first:

```bash
terraform plan -out=tfplan
```

Then apply exactly that saved plan:

```bash
terraform apply tfplan
```

This is useful in controlled CI/CD workflows.

---

# 16. What is `terraform destroy`?

`terraform destroy` removes resources managed by the Terraform configuration.

Command:

```bash
terraform destroy
```

Terraform first determines which managed resources need to be removed and normally asks for confirmation.

This is useful for temporary environments such as labs.

For example:

```text
terraform apply
    ↓
Create EC2
    ↓
Testing
    ↓
terraform destroy
    ↓
Delete EC2
```

**Be extremely careful with `destroy` in production.**

---

# 17. What is Terraform State?

Terraform maintains a **state file** that records information about infrastructure Terraform manages. The default local state file is:

```text
terraform.tfstate
```

State allows Terraform to map the resources declared in your configuration to real infrastructure and understand what it previously created or manages. Terraform uses this information during planning to determine what changes are required.

Think:

```text
Terraform Code
      ↓
Desired State

terraform.tfstate
      ↓
Terraform's record of managed infrastructure
```

---

# 18. Why is State Important?

Suppose your Terraform configuration contains:

```hcl
resource "aws_instance" "web" {
  ...
}
```

Terraform needs to know which actual AWS EC2 instance corresponds to:

```text
aws_instance.web
```

State helps maintain this relationship.

Conceptually:

```text
Terraform resource
        ↓
aws_instance.web
        ↓
Actual EC2 instance
        ↓
instance ID
```

Without state, Terraform would have a much harder time tracking managed infrastructure.

---

# 19. Should You Store `terraform.tfstate` in Git?

Generally, **do not commit Terraform state files containing sensitive or environment-specific information to a public Git repository**. In team environments, Terraform state is commonly stored in a remote backend with access controls and locking.

For example:

```text
Local:
terraform.tfstate

Production/team:
Remote backend
```

For AWS, a common pattern is an S3-based backend combined with a locking mechanism supported by the current Terraform/backend architecture.

---

# 20. What is a Backend?

A Terraform backend determines **where Terraform state is stored and how state operations are handled**. The default is local state, but teams often use a remote backend so multiple engineers and CI/CD systems can work with shared infrastructure state safely.

Conceptually:

```text
Terraform
    ↓
Backend
    ↓
Remote State
```

Benefits include:

```text
Centralized state
Team collaboration
Access control
Recovery
State locking/concurrency control
```

---

# 21. What is State Locking?

State locking prevents multiple Terraform operations from modifying the same state concurrently. Imagine two engineers run `terraform apply` against the same infrastructure at exactly the same time. Without appropriate coordination, their operations could interfere with each other. Remote backends can provide locking or concurrency control depending on the backend.

Think:

```text
Engineer A → Terraform Apply
                    ↓
                 STATE LOCK
                    ↑
Engineer B → waits/rejected
```

This protects the state from conflicting operations.

---

# 22. What is a Variable?

A Terraform variable allows you to make your configuration reusable instead of hardcoding values. Instead of writing:

```hcl
instance_type = "t3.micro"
```

everywhere, you can define:

```hcl
variable "instance_type" {
  type    = string
  default = "t3.micro"
}
```

Then use:

```hcl
instance_type = var.instance_type
```

This allows the same Terraform code to work with different values.

---

# 23. Why Do We Need Variables?

Imagine you have:

```text
Development → t3.micro
Testing     → t3.small
Production  → t3.medium
```

Instead of creating three completely different Terraform configurations, you can use variables.

For example:

```hcl
variable "instance_type" {
  type = string
}
```

Then provide:

```text
t3.micro
```

or:

```text
t3.medium
```

depending on the environment.

---

# 24. What is `terraform.tfvars`?

A `.tfvars` file is commonly used to provide values for Terraform variables.

Example:

```hcl
instance_type = "t3.micro"
region        = "ap-south-1"
```

Then Terraform can automatically load values from files such as:

```text
terraform.tfvars
```

or explicitly:

```bash
terraform apply -var-file="dev.tfvars"
```

This is useful for separating reusable configuration from environment-specific values.

---

# 25. What are Outputs?

Outputs allow Terraform to display useful information after infrastructure is created.

For example, after creating an EC2 instance, you might want to know its public IP.

```hcl
output "instance_public_ip" {
  value = aws_instance.web.public_ip
}
```

After:

```bash
terraform apply
```

Terraform can display the output.

Think:

```text
Resource created
      ↓
Output
      ↓
Useful information
```

---

# 26. What is a Data Source?

A data source allows Terraform to **read information about existing infrastructure or external data** rather than creating that resource.

For example, you may want to find an existing AWS VPC and use its ID when creating a new resource.

Conceptually:

```text
Existing AWS resource
        ↓
Terraform data source
        ↓
Read information
        ↓
Use information in configuration
```

This is different from:

```text
resource → create/manage
data     → read/query
```

---

# 27. Resource vs Data Source

This is a common interview question.

```text
Resource
→ Terraform manages the lifecycle.

Data source
→ Terraform reads existing information.
```

Example:

```hcl
data "aws_vpc" "existing" {
  ...
}
```

Terraform reads information about the existing VPC.

---

# 28. What are Dependencies?

Terraform understands dependencies between resources and creates resources in an appropriate order. For example, if an EC2 instance references a security group, Terraform knows that the security group must exist before the instance can be created.

Example:

```text
Security Group
      ↓
EC2 Instance
```

Terraform builds a dependency graph from references.

---

# 29. Implicit Dependency

An implicit dependency occurs naturally when one resource references another.

Example:

```hcl
resource "aws_security_group" "web" {
  name = "web-sg"
}

resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"

  vpc_security_group_ids = [
    aws_security_group.web.id
  ]
}
```

Because the EC2 instance references:

```text
aws_security_group.web.id
```

Terraform knows the security group must be available first.

---

# 30. Explicit Dependency — `depends_on`

Sometimes Terraform cannot automatically determine a dependency. In those cases, you can explicitly specify one using `depends_on`.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"

  depends_on = [
    aws_iam_role.example
  ]
}
```

This explicitly tells Terraform:

> Create the IAM role before this EC2 resource.

Use `depends_on` only when the dependency isn't already represented by a normal reference.

---

# 31. What is Terraform Module?

A **module** is a reusable collection of Terraform configuration files. Modules allow you to package infrastructure into reusable components. Instead of writing the same VPC, security group, or EC2 configuration repeatedly, you can create a module and use it in different environments.

For example:

```text
modules/
├── vpc/
├── ec2/
└── security-group/
```

Then:

```text
Development → use VPC module
Testing     → use VPC module
Production  → use VPC module
```

---

# 32. Why Do We Need Modules?

Suppose your company creates the same basic VPC architecture for every application:

```text
VPC
├── Public Subnet
├── Private Subnet
├── Internet Gateway
└── Route Tables
```

Instead of copying 500 lines of Terraform code into every project, you can create one reusable module.

Then projects can call:

```hcl
module "vpc" {
  source = "./modules/vpc"
}
```

This improves consistency and reduces duplication.

---

# 33. What is `terraform get` / Module Initialization?

When Terraform configuration uses modules, `terraform init` initializes and downloads the required modules.

For example:

```bash
terraform init
```

may download modules from:

```text
Local directory
Git repository
Terraform Registry
Other supported module sources
```

---

# 34. What is `terraform output`?

After applying infrastructure, you can view outputs using:

```bash
terraform output
```

To retrieve a specific output:

```bash
terraform output instance_public_ip
```

This is useful in scripts and CI/CD pipelines.

---

# 35. What is `terraform show`?

`terraform show` displays the Terraform state or a saved plan in a human-readable form.

Command:

```bash
terraform show
```

For a saved plan:

```bash
terraform show tfplan
```

This is useful when reviewing what Terraform knows about the infrastructure or inspecting a saved plan.

---

# 36. What is `terraform state list`?

This command displays resources currently tracked in Terraform state.

```bash
terraform state list
```

You might see:

```text
aws_instance.web
aws_security_group.web
aws_s3_bucket.logs
```

This is useful when troubleshooting state.

---

# 37. What is `terraform state show`?

This displays detailed state information about one resource.

```bash
terraform state show aws_instance.web
```

It allows you to inspect how Terraform currently represents that resource in state.

---

# 38. What is `terraform refresh`?

In modern Terraform workflows, you generally do not need to use the old standalone `terraform refresh` workflow. Terraform refreshes state information as part of planning and other operations. If you need to reconcile Terraform state with real infrastructure, use normal planning workflows such as:

```bash
terraform plan
```

or:

```bash
terraform plan -refresh-only
```

`-refresh-only` is useful when you want Terraform to update its state based on changes detected in the real infrastructure without proposing normal configuration changes.

---

# 39. What is Drift?

**Drift** occurs when the real infrastructure changes outside Terraform and no longer matches what Terraform configuration and state expect.

For example:

```text
Terraform:
EC2 type = t3.micro

Someone manually changes AWS:
EC2 type = t3.medium
```

Now infrastructure has drifted from the declared configuration.

You can detect potential differences using:

```bash
terraform plan
```

Terraform may show a change required to bring infrastructure back toward the declared configuration.

---

# 40. Terraform Plan Symbols

When you run:

```bash
terraform plan
```

you may see symbols such as:

```text
+ create
~ update in-place
- destroy
-/+ replace
```

Remember:

```text
+  → create
~  → modify
-  → destroy
-/+ → destroy and recreate
```

The `-/+` case is particularly important because changing certain resource attributes requires replacement rather than an in-place update.

---

# 41. What is Resource Replacement?

Sometimes Terraform cannot modify a resource in place because the underlying platform doesn't support that change for that resource. Terraform then plans to destroy the old resource and create a replacement.

Conceptually:

```text
Old Resource
     ↓
Destroy
     ↓
New Resource
```

This is why you should always carefully inspect:

```bash
terraform plan
```

before applying changes to production.

---

# 42. What is `terraform apply -auto-approve`?

Normally:

```bash
terraform apply
```

asks you to confirm.

In automated environments, you may use:

```bash
terraform apply -auto-approve
```

This skips the interactive confirmation.

For example, CI/CD might use it after a plan has already been reviewed or controlled through pipeline approvals.

You should be careful because:

```text
-auto-approve
```

means Terraform won't wait for manual confirmation.

---

# 43. Terraform Authentication with AWS

Terraform needs permission to call AWS APIs. In local development, the AWS provider can use credentials supplied through standard AWS authentication mechanisms, such as the AWS CLI credential/configuration system or environment variables.

For example, you may configure AWS CLI credentials using:

```bash
aws configure
```

Then verify:

```bash
aws sts get-caller-identity
```

If AWS CLI authentication works, the Terraform AWS provider can commonly use the same credential configuration.

Avoid putting permanent access keys directly into Terraform files.

---

# 44. Basic AWS Terraform Example

A simple EC2 configuration could look like:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"

  tags = {
    Name = "terraform-web"
  }
}
```

The flow is:

```text
terraform init
       ↓
terraform validate
       ↓
terraform plan
       ↓
terraform apply
       ↓
EC2 created
```

**Important:** the AMI ID is region-specific, so use a valid AMI ID for your AWS region rather than copying `ami-xxxxxxxx`.

---

# 45. Terraform and AWS VPC

Terraform can create an entire AWS network infrastructure.

For example:

```text
VPC
│
├── Internet Gateway
│
├── Public Subnet
│
├── Private Subnet
│
├── Route Table
│
└── Security Groups
```

Terraform allows you to define this architecture as code so the environment can be reproduced consistently.

---

# 46. Terraform with Docker

Terraform can also manage Docker resources using a Docker provider.

Conceptually:

```text
Terraform
    ↓
Docker Provider
    ↓
Docker API
    ↓
Container
```

Example:

```hcl
resource "docker_image" "nginx" {
  name = "nginx:latest"
}

resource "docker_container" "nginx" {
  name  = "nginx"
  image = docker_image.nginx.image_id

  ports {
    internal = 80
    external = 8080
  }
}
```

Then:

```bash
terraform init
terraform plan
terraform apply
```

Terraform can create the Docker resources described by the configuration.

---

# 47. Terraform with Kubernetes

Terraform can also manage Kubernetes resources using the Kubernetes provider.

For example, Terraform can create:

```text
Namespace
Deployment
Service
ConfigMap
Secret
```

Conceptually:

```text
Terraform
    ↓
Kubernetes Provider
    ↓
Kubernetes API Server
    ↓
Kubernetes Resources
```

This is particularly useful when infrastructure provisioning and Kubernetes environment configuration need to be managed together.

---

# 48. Terraform Workflow

This is the workflow you should memorize:

```text
1. Write Terraform code
          ↓
2. terraform fmt
          ↓
3. terraform init
          ↓
4. terraform validate
          ↓
5. terraform plan
          ↓
6. Review
          ↓
7. terraform apply
          ↓
8. Infrastructure created/updated
          ↓
9. terraform output
```

When you want to remove the environment:

```text
terraform destroy
```

---

# 49. Terraform Project Structure

A simple professional project can look like:

```text
terraform-aws-project/
│
├── providers.tf
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── versions.tf
└── .gitignore
```

### `providers.tf`

Provider configuration.

### `main.tf`

Main infrastructure resources.

### `variables.tf`

Input variables.

### `outputs.tf`

Useful outputs.

### `terraform.tfvars`

Variable values.

### `versions.tf`

Terraform/provider version constraints.

---

# 50. What is `terraform.lock.hcl`?

When Terraform initializes providers, it creates or updates:

```text
.terraform.lock.hcl
```

This file records selected provider versions and checksums so that future installations can use consistent provider packages. It is generally appropriate to commit this lock file to version control.

Think:

```text
terraform.lock.hcl
        ↓
Provider dependency consistency
```

---

# 51. What is `.terraform` Directory?

After:

```bash
terraform init
```

Terraform creates a working directory commonly called:

```text
.terraform/
```

It can contain downloaded provider packages and module-related working data.

You generally don't manually edit this directory.

---

# 52. What is `.gitignore` for Terraform?

You should be careful about what Terraform files you commit to Git. The `.terraform` directory is generally ignored, and local state files should usually not be committed, especially if they may contain sensitive information.

Example:

```gitignore
.terraform/
*.tfstate
*.tfstate.*
*.tfvars
```

However, whether you ignore `.tfvars` depends on whether it contains sensitive values. A non-sensitive example variable file can be committed, while secret-containing files should not be.

The provider lock file is generally committed:

```text
.terraform.lock.hcl
```

---

# 53. What is a Secret in Terraform?

Terraform can manage secrets, but you must understand that simply marking a variable as `sensitive = true` does **not** mean the value is magically absent from state. Sensitive values may still be stored in Terraform state depending on the resource and configuration.

Example:

```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

The `sensitive` setting mainly prevents Terraform from displaying the value in normal CLI output.

For production, protect:

```text
Terraform state
Credentials
Backend
CI/CD variables
Cloud IAM permissions
```

---

# 54. What is a Module Registry?

Terraform modules can be stored in repositories or registries and reused across projects. The Terraform Registry provides publicly available modules and providers. Organizations can also maintain private modules for internal infrastructure standards.

Conceptually:

```text
Module
 ↓
Reusable infrastructure
 ↓
Many projects
```

---

# 55. What is Terraform Workspace?

Terraform workspaces provide a mechanism for managing multiple state instances for a configuration. They can be useful in certain scenarios, especially simple environment separation, but they are not always the best approach for complex production environments.

Commands include:

```bash
terraform workspace list
```

Create:

```bash
terraform workspace new dev
```

Select:

```bash
terraform workspace select dev
```

The key idea is:

```text
Same configuration
       ↓
Different state
```

For larger organizations, separate directories/accounts/backends or other environment strategies are often preferred depending on the architecture.

---

# 56. Terraform and CI/CD

Terraform fits naturally into CI/CD pipelines.

A common pipeline is:

```text
Developer
    ↓
Git Push
    ↓
CI Pipeline
    ↓
terraform fmt
    ↓
terraform validate
    ↓
terraform plan
    ↓
Review / Approval
    ↓
terraform apply
```

This makes infrastructure changes go through the same review and automation process as application code.

---

# 57. Terraform with Git

Terraform configuration should normally be stored in a Git repository.

Example:

```text
Developer
    ↓
Terraform code
    ↓
Git
    ↓
Pull Request
    ↓
Code Review
    ↓
CI
    ↓
Terraform Plan
    ↓
Approval
    ↓
Apply
```

This provides version history and makes infrastructure changes auditable.

---

# 58. Terraform vs Ansible

This is a very common interview question.

Terraform is primarily used for **provisioning and managing infrastructure resources**, while Ansible is commonly used for **configuration management and automation inside systems**.

For example:

```text
Terraform
→ Create EC2
→ Create VPC
→ Create Security Group
→ Create Load Balancer

Ansible
→ Install Nginx
→ Configure Nginx
→ Create application configuration
→ Restart service
```

They can be used together.

```text
Terraform
    ↓
Create infrastructure
    ↓
Ansible
    ↓
Configure servers
```

---

# 59. Terraform vs CloudFormation

Terraform is a multi-provider IaC tool, while AWS CloudFormation is AWS's native infrastructure-as-code service. Terraform can manage resources across AWS, Azure, Google Cloud, Kubernetes, GitHub, and many other systems through providers. CloudFormation is primarily designed around AWS resources and AWS-native integration.

Simple memory:

```text
Terraform
→ Multi-provider

CloudFormation
→ AWS-native
```

---

# 60. Terraform vs Kubernetes

Terraform and Kubernetes solve different problems.

Terraform primarily manages **infrastructure and infrastructure-related resources**, while Kubernetes manages **containerized workloads and their runtime orchestration**.

Example:

```text
Terraform
   ↓
VPC
EC2
EKS
IAM
Load Balancer
```

Then:

```text
Kubernetes
   ↓
Pods
Deployments
Services
ConfigMaps
Ingress
```

They can work together:

```text
Terraform
    ↓
Create EKS infrastructure
    ↓
Kubernetes
    ↓
Run applications
```

---

# 61. What is Terraform Drift Detection?

Drift detection means identifying differences between the Terraform-managed desired configuration and actual infrastructure. Suppose an administrator manually changes an EC2 instance outside Terraform. The next Terraform plan may identify that difference and propose changes to bring the infrastructure back toward the declared configuration.

Example:

```text
Terraform code:
t3.micro

AWS:
t3.medium

       ↓

terraform plan

       ↓

Difference detected
```

This is one reason infrastructure changes should preferably go through Terraform rather than manual console changes.

---

# 62. Common Terraform Errors

### Error 1 — Provider not initialized

You may see an error indicating that a provider is missing or not initialized.

Run:

```bash
terraform init
```

---

### Error 2 — Invalid configuration

Run:

```bash
terraform validate
```

Then inspect the indicated `.tf` file and line.

---

### Error 3 — AWS authentication problem

Test AWS credentials:

```bash
aws sts get-caller-identity
```

If this fails, Terraform may also fail to authenticate with AWS.

---

### Error 4 — Wrong AMI

An AMI may exist in one AWS region but not another.

For example:

```text
AMI from us-east-1
        ↓
Trying to use in ap-south-1
        ↓
Invalid / unavailable
```

Always use an AMI valid for the selected region.

---

# 63. Terraform Troubleshooting Process

When Terraform fails, don't randomly change code.

Use:

```text
1. Read the error
        ↓
2. Identify resource
        ↓
3. Check provider/authentication
        ↓
4. Check region/account
        ↓
5. terraform validate
        ↓
6. terraform plan
        ↓
7. Check state if necessary
        ↓
8. Check cloud-side resource
```

Useful commands:

```bash
terraform validate
terraform plan
terraform show
terraform state list
terraform state show RESOURCE
```

---

# 64. Terraform Import

Sometimes infrastructure already exists but wasn't originally created by Terraform. Terraform supports importing existing infrastructure into state so it can subsequently be managed through Terraform configuration.

Modern Terraform workflows use an `import` block or the `terraform import` command depending on the use case.

Traditional command:

```bash
terraform import aws_instance.web i-0123456789abcdef0
```

Conceptually:

```text
Existing AWS resource
        ↓
Terraform import
        ↓
Terraform state
```

Important: importing a resource into state does not automatically generate the complete correct Terraform configuration for you. You still need appropriate configuration.

---

# 65. Terraform Graph

Terraform builds a dependency graph to determine the order in which resources should be created, changed, or destroyed.

For example:

```text
VPC
 ↓
Subnet
 ↓
Security Group
 ↓
EC2
 ↓
Application
```

Terraform uses resource references and dependencies to construct this graph.

You can visualize the dependency graph with:

```bash
terraform graph
```

---

# 66. Terraform State Commands — Important

These commands are useful when troubleshooting:

```bash
terraform state list
```

Lists resources tracked in state.

```bash
terraform state show aws_instance.web
```

Shows state information for a specific resource.

```bash
terraform show
```

Shows current state or a saved plan in readable form.

Be careful with commands that directly modify state. State manipulation can be dangerous if used incorrectly.

---

# 67. Terraform Practical Project

For your Cloud/DevOps learning, a very useful project is:

## AWS Infrastructure Automation

Create:

```text
                    AWS
                     │
                    VPC
                     │
            ┌────────┴────────┐
            ↓                 ↓
      Public Subnet      Private Subnet
            │                 │
            ↓                 ↓
       Load Balancer       Application
            │
            ↓
           EC2
            │
       Security Group
```

Terraform manages:

```text
VPC
Subnets
Internet Gateway
Route Tables
Security Groups
EC2
S3
IAM
Load Balancer
```

Then:

```text
terraform init
terraform validate
terraform plan
terraform apply
```

After testing:

```bash
terraform destroy
```

This gives you real hands-on experience with Infrastructure as Code.

---

# 68. Terraform + Docker + Kubernetes + Prometheus

You can eventually combine everything you've been learning into one project:

```text
                  Terraform
                     ↓
              AWS Infrastructure
                     ↓
                 Kubernetes
                     ↓
          ┌──────────┴──────────┐
          ↓                     ↓
      Application             Prometheus
          ↓                     ↓
       Containers             Grafana
          ↓
         Logs
          ↓
    Observability Stack
```

This is a strong way to connect your learning instead of studying Docker, Kubernetes, Terraform, and observability as isolated topics.

---

# 69. Most Important Terraform Commands

You should become comfortable with these:

```bash
terraform --version
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
terraform destroy
terraform show
terraform output
terraform state list
terraform state show
terraform providers
terraform graph
terraform workspace list
terraform workspace new
terraform workspace select
terraform import
```

The most important workflow is:

```text
init
 ↓
fmt
 ↓
validate
 ↓
plan
 ↓
apply
```

---

# 70. Terraform Interview Questions

### What is Terraform?

Terraform is an Infrastructure as Code tool that allows infrastructure to be defined and managed using declarative configuration files.

### What is IaC?

Infrastructure as Code is the practice of managing infrastructure through machine-readable configuration rather than manual configuration.

### What is a provider?

A provider is a Terraform plugin that allows Terraform to communicate with an external platform or API.

### What is a resource?

A resource represents infrastructure that Terraform creates and manages.

### What is Terraform state?

Terraform state records information that Terraform uses to map configuration resources to real infrastructure.

### What is `terraform plan`?

It creates a preview of the changes Terraform proposes to make.

### What is `terraform apply`?

It executes the proposed changes and attempts to make infrastructure match the configuration.

### What is `terraform destroy`?

It removes resources managed by Terraform.

### What is a module?

A reusable collection of Terraform configuration.

### What is a data source?

A mechanism for reading information about existing resources or external data.

### What is drift?

Drift is when real infrastructure differs from the configuration/state Terraform expects.

### Terraform vs Ansible?

```text
Terraform → Provision/manage infrastructure
Ansible   → Configure/automate systems
```

---

# 71. Terraform Memory Map

Don't memorize Terraform randomly. Remember this structure:

```text
                         TERRAFORM
                             │
        ┌────────────────────┼────────────────────┐
        ↓                    ↓                    ↓
       IaC               Declarative          HCL
        │                    │                    │
        └────────────────────┼────────────────────┘
                             ↓
                         PROVIDER
                             ↓
                          RESOURCE
                             ↓
                       DEPENDENCY GRAPH
                             ↓
                          STATE
                             ↓
              ┌──────────────┴──────────────┐
              ↓                             ↓
          terraform plan              terraform apply
              ↓                             ↓
           Preview                    Create/Change
                                            ↓
                                      Infrastructure
```

Then:

```text
VARIABLES
    ↓
Reusable values

DATA SOURCES
    ↓
Read existing information

MODULES
    ↓
Reusable infrastructure

OUTPUTS
    ↓
Return useful information

BACKEND
    ↓
Store/manage state

WORKSPACE
    ↓
Separate state instances
```

---

# 72. The Terraform Process You Should Remember

The easiest way to remember Terraform is:

```text
                 WRITE
                   ↓
             Terraform Code
                   ↓
                INIT
                   ↓
          Download Providers
                   ↓
               VALIDATE
                   ↓
            Check Configuration
                   ↓
                 PLAN
                   ↓
          What will change?
                   ↓
                REVIEW
                   ↓
                APPLY
                   ↓
          Infrastructure Changes
                   ↓
                 STATE
                   ↓
        Terraform tracks resources
```

### One-line memory trick:

```text
Terraform = Define → Plan → Apply → Track
```

And the key concepts are:

```text
Provider   → How Terraform talks to a platform

Resource   → What Terraform manages

Variable   → Input

Data       → Read existing information

Module     → Reusable Terraform code

Output     → Result/information

State      → Terraform's record

Backend    → Where/how state is managed

Plan       → Preview

Apply      → Execute

Destroy    → Remove
