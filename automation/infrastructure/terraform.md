# Terraform

An Infrastructure as Code (IaC) tool developed by HashiCorp. It allows you to define, provision, and manage cloud infrastructure using declarative configuration files (`.tf`).

[How to Install Terraform](https://developer.hashicorp.com/terraform/install)

> Official Terraform Cloud-Specific Tutorials
>
> - [AWS](https://developer.hashicorp.com/terraform/tutorials/aws-get-started)
> - [Azure](https://developer.hashicorp.com/terraform/tutorials/azure-get-started)
> - [GCP](https://developer.hashicorp.com/terraform/tutorials/gcp-get-started)
> - [OCI](https://developer.hashicorp.com/terraform/tutorials/oci-get-started)

It can also work with things like **Docker** and **Kubernetes** making it completely "free of vendor lock-in" tool.

## Concepts

### Terraform Blocks

There are 2 *mandatory* blocks in terraform:

- `provider` - They interact with APIs of cloud platforms (like aws, google, azurerm, docker, kubernetes)
- `resource` - The building blocks (like EC2 instances, S3 buckets, VMs)

and 4 *optional* ones

- `data` - Read-only information from provider (e.g., find latest AMI ID dynamically)
- `variable` - Reusable, customizable values.
- `output` - Display values after apply (like IPs, URLs)
- `module` - Groups of resources packaged for reuse which can be local or pulled from a registry (like GitHub).

```hcl
# Configure AWS Provider
provider "aws" {
  region = "us-east-1"
}

# Define a variable for instance type
variable "instance_type" {
  default = "t2.micro"
}

# Retrieve the latest Amazon Linux 2 AMI from AWS
data "aws_ami" "latest_amazon_linux" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}

# Create an EC2 instance using a static AMI (hardcoded)
resource "aws_instance" "my_ec2" {
  ami           = "ami-0c02fb55956c7d316"  # Static Amazon Linux 2 AMI
  instance_type = var.instance_type
}

# Output the public IP of the EC2 instance
output "public_ip" {
  value = aws_instance.my_ec2.public_ip
}

# Use an external module to create a VPC
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "4.0.0"
  name    = "my-vpc"
  cidr    = "10.0.0.0/16"
}

```

### **State**

Terraform tracks the current state of your infrastructure in a file named `terraform.tfstate`.

> **Never edit this manually**

### Terraform Workflow Lifecycle

| Step | Command             | Description                            |
| ---- | ------------------- | -------------------------------------- |
| 1    | `terraform init`    | Initializes Terraform in a directory   |
| 2    | `terraform plan`    | Shows what Terraform will do (preview) |
| 3    | `terraform apply`   | Applies the changes (provisions)       |
| 4    | `terraform destroy` | Tears down all managed infrastructure  |

## 🗂️ Terraform File Types

| File                 | Purpose                                        |
| -------------------- | ---------------------------------------------- |
| `*.tf`               | Main config files (provider, resources, etc.)  |
| `variables.tf`       | Define input variables (optional)              |
| `terraform.tfvars`   | Supply actual values for variables             |
| `outputs.tf`         | Define outputs from the module                 |
| `.terraform/`        | Internal cache, modules/plugins                |
| `terraform.tfstate`  | Tracks infrastructure state                    |
| `terraform.lock.hcl` | Dependency versions (like `package-lock.json`) |

---

## ☁️ Supported Providers (Popular Examples)

* AWS
* Azure
* Google Cloud
* Kubernetes
* Docker
* DigitalOcean
* Cloudflare
* GitHub
* Proxmox (community)

---

## ✅ Advantages

* **Cloud-agnostic**
* **Version-controlled infrastructure**
* **Idempotent** (reapplying won’t break things)
* **Automates provisioning**
* Large **module registry** for reuse

---

## ⚠️ Best Practices

* Use **variables** for flexibility
* Use **remote state** (e.g., S3 + DynamoDB for AWS)
* Commit `.tf` files, **not `tfstate`**
* **Modularize** for large projects
* Use `terraform plan` before `apply`
* Protect sensitive variables with `.tfvars` + `.gitignore`

---

## 🧠 Example Use Cases

* Launching AWS EC2 instances
* Creating entire VPCs with subnets
* Managing Kubernetes clusters
* Provisioning Docker containers
* Automating Cloudflare DNS records
* Setting up GitHub repositories and teams

---

Would you like a **cheat sheet**, **project structure template**, or **tutorial series** (e.g., from EC2 → Load Balancer → Auto Scaling)?

