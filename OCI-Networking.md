# Oracle Cloud Infrastructure (OCI) Networking Guide

## **1. Virtual Cloud Network (VCN)**
### **Overview**
A **Virtual Cloud Network (VCN)** in OCI is a customizable private network similar to a traditional data center network. It is the foundation for deploying cloud resources and consists of multiple subnets, route tables, security lists, and gateways.

### **VCN Components:**
- **Subnets:** Logical subdivisions of a VCN. Can be **public** (internet accessible) or **private** (restricted to internal traffic).
- **Security Lists & NSG (Network Security Groups):** Define inbound and outbound traffic rules.
- **Route Tables:** Direct network traffic to its appropriate destination.
- **Dynamic Routing Gateway (DRG):** Allows communication with other VCNs or on-premises networks.
- **Internet Gateway (IGW):** Provides access to and from the internet for public subnets.
- **Service Gateway (SGW):** Enables private access to OCI services like Object Storage.
- **Local and Remote Peering:** Connects VCNs within or across regions.
- **DNS (Domain Name System):** Resolves domain names to IP addresses within OCI.

### **VCN Configuration Steps**
#### **Using OCI Console:**
1. Navigate to **Networking > Virtual Cloud Networks**.
2. Click **Create VCN**.
3. Provide **VCN Name**, **Compartment**, and **CIDR Block** (e.g., `10.0.0.0/16`).
4. Select **DNS Resolution** options.
5. Click **Create VCN**.

#### **Using OCI CLI:**
```sh
oci network vcn create --compartment-id <compartment_OCID> --cidr-block "10.0.0.0/16" --display-name "MyVCN"
```

#### **Using Terraform:**
```hcl
resource "oci_core_vcn" "my_vcn" {
  cidr_block = "10.0.0.0/16"
  display_name = "MyVCN"
  compartment_id = var.compartment_ocid
}
```

## **2. Subnets**
### **Overview**
A **Subnet** is a logical subdivision of a VCN. It can be **public** (accessible via the Internet) or **private** (restricted access).

### **Subnet Configuration Steps**
#### **Using OCI Console:**
1. Go to **Networking > Subnets**.
2. Click **Create Subnet**.
3. Select the **VCN**, provide a **Name**, and specify the **CIDR block** (e.g., `10.0.1.0/24`).
4. Choose **Public or Private**.
5. Associate with a **Route Table and Security List**.
6. Click **Create Subnet**.

#### **Using OCI CLI:**
```sh
oci network subnet create --vcn-id <vcn_OCID> --cidr-block "10.0.1.0/24" --display-name "MySubnet" --prohibit-public-ip-on-vnic false
```

#### **Using Terraform:**
```hcl
resource "oci_core_subnet" "my_subnet" {
  cidr_block = "10.0.1.0/24"
  display_name = "MySubnet"
  vcn_id = oci_core_vcn.my_vcn.id
}
```

## **3. Security List & Network Security Group (NSG)**
### **Overview**
- **Security List:** Applied at the **Subnet level**.
- **NSG (Network Security Group):** Applied at the **VNIC (instance) level** for fine-grained security.

### **Configuration Steps**
#### **Using OCI Console:**
1. Go to **Networking > Security Lists** or **Network Security Groups**.
2. Click **Create** and define **Ingress and Egress rules**.
3. Associate the security group with the appropriate **VCN or subnet**.

#### **Using OCI CLI:**
```sh
oci network security-list create --vcn-id <vcn_OCID> --display-name "MySecurityList" --egress-security-rules '[{"protocol":"all","destination":"0.0.0.0/0"}]'
```

#### **Using Terraform:**
```hcl
resource "oci_core_security_list" "my_security_list" {
  vcn_id = oci_core_vcn.my_vcn.id
}
```

## **4. Route Table**
### **Overview**
Route Tables contain rules that direct traffic within the VCN and to external networks.

### **Configuration Steps**
#### **Using OCI Console:**
1. Go to **Networking > Route Tables**.
2. Click **Create Route Table**.
3. Add **Route Rules** (e.g., route all `0.0.0.0/0` traffic to the **Internet Gateway**).
4. Associate with a **subnet**.

#### **Using OCI CLI:**
```sh
oci network route-table create --vcn-id <vcn_OCID> --route-rules '[{"destination":"0.0.0.0/0", "networkEntityId":"<ig_OCID>"}]'
```

#### **Using Terraform:**
```hcl
resource "oci_core_route_table" "my_route_table" {
  vcn_id = oci_core_vcn.my_vcn.id
}
```

## **5. VNIC (Virtual Network Interface Card)**
### **Overview**
A **VNIC** connects an instance to a subnet. Each Compute instance has at least one VNIC.

### **Configuration Steps**
#### **Using OCI Console:**
1. Navigate to the **Compute Instance**.
2. Click **Attach VNIC**.
3. Select **Subnet** and configure **Private or Public IP**.
4. Click **Attach VNIC**.

#### **Using OCI CLI:**
```sh
oci compute vnic-attach --instance-id <instance_OCID> --subnet-id <subnet_OCID>
```

#### **Using Terraform:**
```hcl
resource "oci_core_vnic_attachment" "my_vnic" {
  instance_id = oci_core_instance.my_instance.id
  subnet_id = oci_core_subnet.my_subnet.id
}
```

## **6. DNS in OCI**
### **Overview**
OCI provides DNS services for **private and public domain resolution**.

### **Configuration Steps**
#### **Using OCI Console:**
1. Go to **Networking > DNS Zones**.
2. Click **Create DNS Zone**.
3. Provide **Domain Name** and **Zone Type** (Private/Public).
4. Configure DNS records (A, CNAME, etc.).
5. Click **Create Zone**.

#### **Using OCI CLI:**
```sh
oci dns zone create --name "example.com" --zone-type "PRIMARY" --compartment-id <compartment_OCID>
```

#### **Using Terraform:**
```hcl
resource "oci_dns_zone" "my_dns" {
  name = "example.com"
  zone_type = "PRIMARY"
}
```

## **7. Network Visualizer**
### **Overview**
Network Visualizer provides a **graphical representation** of VCN components.

### **Steps to Use:**
1. Go to **Networking > Network Visualizer**.
2. Select the **VCN** to analyze.
3. Review **security rules, route tables, and peering connections**.

---
---

## Connectivity Options

OCI provides multiple methods to connect your on-premises network with your VCN, ensuring secure and reliable communication.

### Site-to-Site VPN

Site-to-Site VPN establishes encrypted IPsec tunnels over the internet between your on-premises network and OCI. OCI creates redundant tunnels for each VPN connection to enhance availability. This option is suitable for lower-bandwidth requirements or as a backup to other connections.

**Setting Up Site-to-Site VPN:**

1. **Create a Dynamic Routing Gateway (DRG):**

   - In the OCI Console, navigate to 'Networking' > 'Dynamic Routing Gateways'.

   - Click 'Create DRG', provide a name, and select the compartment.

   - Click 'Create DRG'.

2. **Attach the DRG to Your VCN:**

   - Go to your VCN details page.

   - Click 'Attach DRG' and select the DRG you created.

3. **Configure the Customer-Premises Equipment (CPE):**

   - Navigate to 'Networking' > 'Customer-Premises Equipment'.

   - Click 'Create CPE', enter a name and the public IP address of your on-premises VPN device.

4. **Create the IPSec Connection:**

   - Go to 'Networking' > 'IPSec Connections'.

   - Click 'Create IPSec Connection', provide a name, select the DRG and CPE, and configure routing type.

5. **Configure Your On-Premises VPN Device:**

   - Use the provided configuration details to set up your VPN device.

   - OCI offers configuration guides for various devices to assist with this process.

### Oracle Cloud Infrastructure FastConnect

FastConnect provides a dedicated, private connection between your on-premises network and OCI, bypassing the public internet. It offers higher bandwidth options and a more consistent networking experience.

**FastConnect Connectivity Models:**

- **With an Oracle Provider:** Establish a FastConnect connection through an Oracle partner that offers cloud connectivity services.

- **With a Third-Party Provider:** Use a non-Oracle network carrier to establish a private circuit to OCI.

**Setting Up FastConnect:**

1. **Create a DRG:**

   - Follow the same steps as in the Site-to-Site VPN setup to create and attach a DRG to your VCN.

2. **Initiate FastConnect Setup:**

   - Navigate to 'Networking' > 'FastConnect'.

   - Click 'Create Connection' and choose the appropriate provider type.

3. **Provide Connection Details:**

   - Enter necessary information such as location, port speed, and routing policy.

4. **Coordinate with Your Provider:**

   - Work with your chosen provider to establish the physical connection.

5. **Configure Virtual Circuits:**

   - Set up virtual circuits to define the logical connection over the physical link.

## Edge Services

OCI's edge services enhance performance, security, and availability for applications and data.

### Oracle Roving Edge Infrastructure

Roving Edge Infrastructure extends OCI services to remote locations, enabling high-performance computing and storage capabilities even in environments with limited or no internet connectivity. It's ideal for scenarios requiring local data processing before syncing with the cloud.

**Using Roving Edge Infrastructure:**

1. **Order Roving Edge Devices (REDs):**

   - Access the OCI Console and navigate to 'Roving Edge Infrastructure'.

   - Request the desired number of REDs, specifying configurations and workloads.

2. **Receive and Deploy REDs:**

   - Once delivered, set up the devices at your remote location.

   - Power on and configure network settings to integrate with your local environment.

3. **Synchronize Data with OCI:**

   - When connectivity is available, sync data between the REDs and your OCI tenancy.

### Web Application Firewall (WAF)

OCI Web Application Firewall (WAF) provides protection for web applications by filtering and monitoring HTTP traffic. It helps prevent threats such as SQL injection, cross-site scripting (XSS), and DDoS attacks.

**Key Features of OCI WAF:**
- Protects against OWASP Top 10 vulnerabilities.
- Provides customizable rule sets and rate-limiting policies.
- Can be integrated with OCI Load Balancer and API Gateway.

**Steps to Configure OCI WAF:**
1. **Navigate to the OCI Console** and go to **Security → Web Application Firewall**.
2. Click **Create Web Application Firewall Policy**.
3. Provide a **name** and **compartment**.
4. Choose the **WAF Type** (Edge or Regional).
5. Configure **Access Control Rules** to allow or block specific IPs.
6. Set up **Protection Rules** (e.g., enable SQL injection prevention).
7. Link WAF with your **Load Balancer or API Gateway**.
8. Click **Create** and apply the policy.

### Traffic Management Steering Policies

Traffic Management helps optimize traffic flow by directing requests to the best performing or geographically closest endpoint.

**Steering Policy Types:**
- **Failover:** Routes traffic to a backup endpoint if the primary is down.
- **Load Balancer:** Distributes traffic evenly across multiple endpoints.
- **Geolocation:** Routes users to the nearest regional endpoint.
- **ASN/Prefix-based Steering:** Directs users based on ISP.

**Steps to Create a Steering Policy:**
1. In the **OCI Console**, go to **Networking → Traffic Management**.
2. Click **Create Steering Policy**.
3. Choose a **policy type** (Failover, Load Balancing, Geolocation).
4. Define the **endpoints** (e.g., multiple OCI regions or on-premise).
5. Set **health checks** to monitor availability.
6. Click **Create Steering Policy** and attach it to a **DNS Zone**.

---

## **Advanced Networking Concepts in OCI**

### **Cross-Region VCN Peering**
VCN Peering allows private connectivity between two VCNs, either in the same region (Local Peering) or across regions (Remote Peering).

**Steps to Set Up Remote VCN Peering:**
1. **Create a DRG** in both regions and attach it to the VCN.
2. **Set up IAM policies** to allow communication between compartments.
3. In the first region, create a **Remote Peering Connection (RPC)** under the DRG.
4. Copy the RPC OCID and use it to establish a peering connection in the second region.
5. Update **Route Tables** in both VCNs to route traffic through the DRG.
6. Verify connectivity using **ping** or **traceroute**.

### **Private Endpoint Services**
Private Endpoints allow accessing OCI services privately over the VCN without exposure to the public internet.

**Creating a Private Endpoint:**
1. Go to **Networking → Private Endpoints**.
2. Click **Create Private Endpoint**.
3. Select the **target service** (e.g., Object Storage, Database).
4. Choose the **VCN and subnet** for the endpoint.
5. Assign **Security Groups** and **Route Tables** to control access.
6. Click **Create** and test connectivity using **private DNS resolution**.

---

## **Security in OCI Networking**
### **Network Security Groups (NSG) vs Security Lists**
- **NSG:** Attaches to individual **compute instances** and provides fine-grained security control.
- **Security List:** Applies to an **entire subnet**, impacting all resources inside.

**Creating an NSG:**
1. Go to **Networking → Network Security Groups**.
2. Click **Create NSG** and assign it to a **VCN**.
3. Add **Ingress and Egress Rules** (e.g., allow TCP 443 for HTTPS).
4. Associate the NSG with **Compute Instances** or **Load Balancers**.

### **Bastion Service**
The Bastion Service provides secure SSH access to private instances without exposing them to the internet.

**Steps to Create a Bastion:**
1. Go to **Identity & Security → Bastion**.
2. Click **Create Bastion**, assign it to a **VCN and Subnet**.
3. Define the **allowed IPs** for SSH access.
4. Click **Create Session**, enter the **target private instance**.
5. Use OCI **Cloud Shell or SSH proxy** to connect.

---

## **Monitoring and Troubleshooting OCI Networking**
### **Network Visualizer**
The Network Visualizer helps identify misconfigured network components by providing a **graphical view** of VCNs, subnets, and connections.

**Using Network Visualizer:**
1. Go to **Networking → Network Visualizer**.
2. Select the **VCN** to analyze.
3. Review **network paths, security rules, and peering connections**.

### **VPC Flow Logs**
VPC Flow Logs record network traffic, helping troubleshoot connectivity issues.

**Enabling VPC Flow Logs:**
1. Go to **Networking → Flow Logs**.
2. Click **Create Flow Log**.
3. Select the **VCN and Subnet** to monitor.
4. Store logs in **OCI Object Storage**.
5. Analyze logs using **Logging Analytics**.

---

## **Conclusion**
OCI offers a robust networking stack with multiple connectivity, security, and edge solutions for enterprises of all sizes. Understanding these services enables architects and engineers to build **scalable, secure, and high-performance** cloud environments.
