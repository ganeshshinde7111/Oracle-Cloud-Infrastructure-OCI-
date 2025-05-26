#  Terraform with OCI (Oracle Cloud Infrastructure)
---

### 🔰 PART 1: INTRODUCTION TO OCI AND TERRAFORM

---

#### **1.1 What is OCI (Oracle Cloud Infrastructure)?**

* OCI is Oracle’s Gen 2 cloud platform.
* Key services: Compute, Networking, Storage, Identity & Access Management (IAM), Databases, Security, etc.
* Global footprint with **regions, ADs (Availability Domains), and Fault Domains**.
* OCI follows **compartment-based architecture** for isolation and resource governance.

#### **1.2 What is Terraform?**

* Terraform is an open-source **Infrastructure as Code (IaC)** tool by HashiCorp.
* Enables **provisioning and managing cloud resources declaratively** using `.tf` configuration files.
* Follows **Declarative syntax + Provider model**.

---

### 🔹 PART 2: OCI PROVIDER IN TERRAFORM

---

#### **2.1 What is a Provider in Terraform?**

* A **provider** is a plugin to interact with APIs (OCI in our case).
* OCI Provider enables Terraform to manage Oracle Cloud resources.

#### **2.2 OCI Provider Configuration**

```hcl
provider "oci" {
  tenancy_ocid     = var.tenancy_ocid
  user_ocid        = var.user_ocid
  fingerprint      = var.fingerprint
  private_key_path = var.private_key_path
  region           = var.region
}
```

* Required authentication details include:

  * `tenancy_ocid`, `user_ocid`, `private_key_path`, `fingerprint`, and `region`
* These can also be configured via `config` file (`~/.oci/config`)

---

### 🔹 PART 3: BASIC MODULES AND RESOURCE CREATION

---

#### **3.1 VCN and Subnets (Networking)**

**VCN (Virtual Cloud Network) creation:**

```hcl
resource "oci_core_virtual_network" "my_vcn" {
  cidr_block     = "10.0.0.0/16"
  compartment_id = var.compartment_id
  display_name   = "my-vcn"
}
```

**Subnet:**

```hcl
resource "oci_core_subnet" "my_subnet" {
  cidr_block        = "10.0.1.0/24"
  vcn_id            = oci_core_virtual_network.my_vcn.id
  compartment_id    = var.compartment_id
  availability_domain = var.ad
  display_name      = "my-subnet"
}
```

---

#### **3.2 Compute Instance**

```hcl
resource "oci_core_instance" "example" {
  availability_domain = var.ad
  compartment_id      = var.compartment_id
  shape               = "VM.Standard2.1"
  display_name        = "example-instance"

  create_vnic_details {
    subnet_id = oci_core_subnet.my_subnet.id
  }

  source_details {
    source_type = "image"
    image_id    = var.image_id
  }

  metadata = {
    ssh_authorized_keys = file("~/.ssh/id_rsa.pub")
  }
}
```

---

#### **3.3 IAM: Users, Groups, Policies**

You cannot create users via Terraform due to OCI limitations (for now) unless using **OCI Identity Domains**.

**Example: Policy Creation**

```hcl
resource "oci_identity_policy" "my_policy" {
  name           = "AllowGroupToManageVCN"
  description    = "Policy to allow group to manage VCN"
  compartment_id = var.tenancy_ocid
  statements = [
    "Allow group NetworkAdmins to manage virtual-network-family in tenancy"
  ]
}
```

---

---

## 🔷 PART 4: INTERMEDIATE MODULES AND BEST PRACTICES

---

### **4.1 Using Modules in Terraform**

**Why use Modules?**

* Encapsulation of resources
* Reusability
* Cleaner and maintainable code

**Module structure example (VCN Module):**

**`modules/vcn/main.tf`**

```hcl
resource "oci_core_virtual_network" "vcn" {
  cidr_block     = var.cidr_block
  display_name   = var.display_name
  compartment_id = var.compartment_id
}
```

**`modules/vcn/variables.tf`**

```hcl
variable "cidr_block" {}
variable "display_name" {}
variable "compartment_id" {}
```

**Root module:**

```hcl
module "vcn" {
  source         = "./modules/vcn"
  cidr_block     = "10.0.0.0/16"
  display_name   = "my-vcn"
  compartment_id = var.compartment_id
}
```

---

### **4.2 OCI Resource Dependency Management**

* Terraform uses a **graph-based dependency engine**
* Resources reference others via `.id` or output
* Use `depends_on` for explicit ordering

---

### **4.3 Managing State Files**

* **Terraform State File (`terraform.tfstate`)** keeps track of deployed resources
* **Remote state** is preferred for teams: Use OCI Object Storage backend

**Backend Example:**

```hcl
terraform {
  backend "oci" {
    namespace = "mytenancynamespace"
    bucket    = "terraform-state"
    key       = "terraform.tfstate"
    region    = "us-ashburn-1"
  }
}
```

---

### **4.4 Variables and Outputs**

**Variables:**

```hcl
variable "compartment_id" {
  description = "Compartment OCID"
  type        = string
}
```

**Outputs:**

```hcl
output "vcn_id" {
  value = oci_core_virtual_network.vcn.id
}
```

Use `terraform output` to retrieve values after provisioning.

---

### **4.5 Workspaces in Terraform**

* Used for **managing multiple environments** (dev, test, prod)

```bash
terraform workspace new dev
terraform workspace select dev
```

---

### **4.6 Terraform CLI Workflow**

1. `terraform init` → Initialize provider and backend
2. `terraform plan` → Preview changes
3. `terraform apply` → Apply changes
4. `terraform destroy` → Tear down infra

---

### **4.7 Dynamic Blocks and Conditionals**

**Dynamic Block:**

```hcl
dynamic "egress_security_rules" {
  for_each = var.egress_rules
  content {
    protocol = egress_security_rules.value.protocol
    # More attributes...
  }
}
```

**Conditionals:**

```hcl
resource "oci_core_instance" "optional_instance" {
  count = var.create_instance ? 1 : 0
  # ...
}
```

---

## 🔷 PART 5: ADVANCED CONCEPTS IN TERRAFORM WITH OCI

---

### **5.1 Resource Lifecycle Management**

```hcl
resource "oci_core_instance" "example" {
  # ...
  lifecycle {
    prevent_destroy = true
    ignore_changes  = [metadata]
  }
}
```

---

### **5.2 OCI DRG, FastConnect, and VPN Setup**

DRG:

```hcl
resource "oci_core_drg" "example" {
  compartment_id = var.compartment_id
  display_name   = "example-drg"
}
```

FastConnect / VPN setups involve:

* DRG Attachment
* Route Tables
* Security Lists
* IPSEC Tunnels (for VPN)

---

### **5.3 OCI Load Balancer, Autoscaling and Monitoring**

**Load Balancer:**

```hcl
resource "oci_load_balancer" "lb" {
  compartment_id = var.compartment_id
  shape          = "100Mbps"
  subnet_ids     = [var.subnet_id]
}
```

**Auto Scaling Configuration** (via Monitoring + Instance Pool):

* Use Terraform to define instance configuration + pool
* Attach Monitoring Alarms and Policies

---

### **5.4 OCI Vault and Secrets**

Manage secrets (passwords, API keys) securely:

```hcl
resource "oci_vault_secret" "db_password" {
  compartment_id = var.compartment_id
  secret_name    = "db-password"
  vault_id       = var.vault_id
  secret_content {
    content_type = "BASE64"
    content      = base64encode("mysecretpassword")
  }
}
```

---

### **5.5 Terraform Automation with CI/CD**

Integrate Terraform into:

* **OCI DevOps Pipelines**
* **Jenkins**
* **GitHub Actions**
* Use `terraform plan` + `terraform apply` in secure automation workflows

---

---

## 🔐 PART 6: SECURITY, GOVERNANCE & COMPLIANCE WITH TERRAFORM

---

### **6.1 OCI IAM Best Practices (with Terraform)**

* **Least privilege principle**: Only give what’s needed
* Use Terraform to manage **Policies**, **Groups**, **Dynamic Groups**, and **Compartments**

**Example: Dynamic Group + Policy (for instances to access Object Storage)**

```hcl
resource "oci_identity_dynamic_group" "instance_dg" {
  name           = "instance-dg"
  description    = "Dynamic group for compute instance access"
  matching_rule  = "ALL {instance.compartment.id = 'ocid1.compartment.oc1..xyz'}"
}

resource "oci_identity_policy" "dg_policy" {
  name           = "InstanceDGPolicy"
  compartment_id = var.tenancy_ocid
  statements = [
    "Allow dynamic-group instance-dg to use object-family in tenancy"
  ]
}
```

---

### **6.2 OCI Tags, Quotas, and Budgets**

**Tagging Example:**

```hcl
resource "oci_identity_tag_namespace" "project_ns" {
  compartment_id = var.tenancy_ocid
  name           = "project"
  description    = "Namespace for project tags"
}
```

**Quotas Example:**

```hcl
resource "oci_limits_quota" "restrict_shape" {
  compartment_id = var.compartment_id
  name           = "restrict-shape"
  statements = [
    "zero virtual-machine-count in AD-1 where request.shape = 'VM.Standard2.1'"
  ]
}
```

---

### **6.3 Compartment Hierarchy & Resource Isolation**

* Organize resources using **nested compartments**
* Use Terraform modules to deploy per compartment

---

## ⚙️ PART 7: ADVANCED REAL-WORLD SCENARIOS

---

### **7.1 Multi-Region & DR Setup**

* Define modules for each region
* Use `provider alias` for secondary region

```hcl
provider "oci" {
  alias           = "dr"
  region          = "uk-london-1"
  tenancy_ocid    = var.tenancy_ocid
  # other auth...
}
```

---

### **7.2 Autoscaling Compute Instances via Terraform**

* Create Instance Configuration
* Launch into Instance Pool
* Define Auto Scaling Policy based on metrics

---

### **7.3 Hybrid Cloud Setup (VPN, FastConnect)**

* Use Terraform to provision:

  * DRG
  * IPSEC Connection
  * Customer-Premises Equipment (CPE)
  * Routing
  * NAT Gateway or Internet Gateway

---

### **7.4 Using Data Sources**

Example: Use existing resources

```hcl
data "oci_identity_availability_domains" "ads" {
  compartment_id = var.tenancy_ocid
}
```

---

### **7.5 Using External Providers & Null Resources**

* For scripts or custom actions:

```hcl
resource "null_resource" "configure_vm" {
  provisioner "remote-exec" {
    connection {
      host        = self.public_ip
      user        = "opc"
      private_key = file("~/.ssh/id_rsa")
    }
    inline = [
      "sudo yum install nginx -y",
      "sudo systemctl start nginx"
    ]
  }
}
```

---

## 🧰 PART 8: TROUBLESHOOTING & DEBUGGING

---

### **8.1 Common Issues**

| Problem                     | Fix                                                    |
| --------------------------- | ------------------------------------------------------ |
| Auth failure                | Validate OCIDs, region, and key format                 |
| Missing permissions         | Check OCI IAM Policy or user group                     |
| Terraform plan/apply errors | Use `terraform validate`, `plan` and debug logs        |
| Resource already exists     | Use `terraform import` or manually delete and re-apply |
| Drift from state            | Use `terraform refresh` or re-import resources         |

---

### **8.2 Useful Terraform CLI Commands**

* `terraform graph` – visualize dependency graph
* `terraform show` – show current state
* `terraform console` – query state interactively
* `terraform taint` – force recreate resource
* `terraform import` – import existing resource into state

---

## 🎯 PART 9: INTERVIEW PREP – MUST-KNOW TOPICS & QUESTIONS

---

### ✅ **OCI + Terraform Topics to Master**

* Provider authentication flow
* Resource dependencies
* Reusable modules
* Lifecycle management
* CI/CD pipeline integration
* Security and policies
* Remote backend and collaboration
* State file internals
* Real-world deployments (DR, autoscaling, networking)
* Multi-region deployments
* Secrets & Vault integration
* Quotas, Budgets, Tagging strategies

---

### 🧠 **Example Interview Questions**

#### *Beginner-Level*

* What is OCI and what is Terraform?
* How do you authenticate Terraform with OCI?
* How do you create a VCN and Subnet using Terraform?

#### *Intermediate-Level*

* How do you manage state files securely?
* What are modules and why are they useful?
* What is the difference between data and resource blocks?

#### *Advanced-Level*

* How do you handle multi-region deployments?
* How do you integrate Terraform into OCI DevOps pipelines?
* How do you provision a DRG with FastConnect and Route Tables?
* What steps would you take to avoid state drift?
* How do you ensure secure key/secret management with Terraform?

---

---

## 🟢 BEGINNER LEVEL MOCK INTERVIEW Q\&A

---

### **Q1: What is Terraform and how does it relate to OCI?**

**A:**
Terraform is an open-source infrastructure-as-code tool developed by HashiCorp that allows users to define and provision cloud infrastructure using a declarative configuration language. In the context of OCI (Oracle Cloud Infrastructure), Terraform uses the **OCI provider plugin** to interact with Oracle’s APIs and create resources such as compute instances, VCNs, and storage services programmatically.

---

### **Q2: How do you authenticate Terraform with OCI?**

**A:**
Terraform uses OCI’s CLI configuration file (`~/.oci/config`) for authentication, which includes values like:

* `tenancy_ocid`
* `user_ocid`
* `fingerprint`
* `private_key_path`
* `region`

Alternatively, these can be passed as environment variables or directly defined in the provider block of the `.tf` file.

---

### **Q3: How do you create a simple compute instance in OCI using Terraform?**

**A:**
To create a compute instance:

1. Define a VCN and subnet.
2. Use the `oci_core_instance` resource.
3. Provide required details like shape, image ID, and networking configuration.
4. Attach an SSH public key for remote access via the `metadata` block.

---

### **Q4: What is a Terraform state file and why is it important?**

**A:**
The state file (`terraform.tfstate`) keeps track of all the resources that Terraform manages. It maps Terraform configurations to real-world infrastructure. It’s crucial for tracking resource dependencies, ensuring idempotency, and enabling updates and destruction of infrastructure without drift.

---

## 🔵 INTERMEDIATE LEVEL MOCK INTERVIEW Q\&A

---

### **Q5: What are modules in Terraform, and how do you use them in OCI?**

**A:**
Modules in Terraform are containers for multiple resources that are grouped together to be reused. In OCI, for example, a VCN module might create a VCN, internet gateway, route tables, and subnets. Modules promote DRY (Don’t Repeat Yourself) coding practices and help in managing complex infrastructure across environments.

---

### **Q6: How do you manage different environments like dev, test, and prod using Terraform?**

**A:**
This can be achieved using:

* **Terraform Workspaces** (`terraform workspace new dev`)
* Separate variable files (`dev.tfvars`, `prod.tfvars`)
* Using modules and passing environment-specific variables
* Directory structure for isolated configurations per environment

---

### **Q7: How do you ensure security when using Terraform with OCI?**

**A:**
Security practices include:

* Storing state files securely (OCI Object Storage with encryption)
* Using OCI Vault for secrets instead of hardcoding sensitive values
* Leveraging IAM policies to control access to Terraform users and groups
* Enabling `prevent_destroy` in critical resources

---

### **Q8: How would you provision Object Storage buckets and secure access using Terraform?**

**A:**

1. Create a bucket using `oci_objectstorage_bucket`.
2. Use IAM policies to allow users or dynamic groups to access it.
3. Optionally, configure pre-authenticated requests or use access policies.

---

### **Q9: Explain how Terraform handles dependencies between resources.**

**A:**
Terraform builds a dependency graph internally. Dependencies are inferred from references. For example, if a subnet depends on a VCN (`vcn_id = oci_core_virtual_network.vcn.id`), Terraform ensures the VCN is created first. `depends_on` can be used explicitly when needed.

---

## 🔴 ADVANCED LEVEL MOCK INTERVIEW Q\&A

---

### **Q10: How do you manage multi-region infrastructure with Terraform in OCI?**

**A:**
Use **provider aliases** for each region. Define multiple provider blocks with different regions and assign resources to specific regions using `provider = oci.<alias>`. This enables deploying resources like compute, storage, and DRGs in multiple regions for DR or latency optimization.

---

### **Q11: How would you design and automate a secure, auto-scaled web application in OCI using Terraform?**

**A:**

1. Create:

   * VCN + subnets (public/private)
   * Internet gateway/NAT
   * Load balancer
   * Instance pool with instance configuration
   * Auto scaling policies
2. Use OCI Monitoring to trigger auto-scaling.
3. Secure communication with security lists and OCI Vault.
4. Modularize the design.
5. Use CI/CD pipeline to automate deployments.

---

### **Q12: How would you handle drift detection in Terraform-managed infrastructure?**

**A:**

* Run `terraform plan` to detect drift.
* Use `terraform refresh` to sync state with actual infrastructure.
* For manual changes outside Terraform, use `terraform import` to bring them under management.
* Enable strict policies to avoid ad-hoc changes outside Terraform.

---

### **Q13: How do you use Terraform to provision a DRG and configure VPN connectivity?**

**A:**

1. Create a DRG.
2. Create a CPE for on-prem connectivity.
3. Create an IPSEC connection.
4. Attach the DRG to VCN.
5. Update Route Tables to route traffic through DRG.
6. Use security lists for appropriate port/protocol access.

---

### **Q14: What are some best practices for collaborating on Terraform code in a team setting?**

**A:**

* Use **remote backend (OCI Object Storage)** with state locking (via DynamoDB or custom solutions)
* Store code in version control (Git)
* Code reviews and approvals via pull requests
* Use `terraform fmt` and `terraform validate`
* Use environment variables to avoid hardcoding sensitive data
* Tag and document resources

---

### **Q15: How do you integrate Terraform into a CI/CD pipeline in OCI?**

**A:**

* Use **OCI DevOps Pipelines**, GitHub Actions, GitLab CI, or Jenkins.
* Pipeline steps:

  * `terraform init`
  * `terraform validate`
  * `terraform plan -out=tfplan`
  * Approval step (manual or automated)
  * `terraform apply tfplan`
* Store secrets and environment variables securely

---
