

---

## **1. OCI Storage Solutions Overview**

OCI provides the following storage options:

# **1. Object Storage (Standard & Archive Storage)**  

## **Overview:**  
OCI Object Storage is an **internet-scale, highly durable storage service** designed to store unstructured data such as backups, logs, images, and videos. It supports:  
- **Standard Storage**: For frequently accessed data (hot storage).  
- **Archive Storage**: For rarely accessed data (cold storage) at a lower cost.  

## **Key Features:**  
✔ **Unlimited Scalability** – No limit on storage capacity.  
✔ **Durability & Availability** – Data is replicated across availability domains for protection.  
✔ **Encryption & Security** – Data is encrypted in transit and at rest.  
✔ **Lifecycle Management** – Auto-move objects between Standard and Archive tiers.  

# **2. Block Volume (Persistent Storage for Compute Instances)**  

## **Overview:**  
OCI Block Volume provides **high-performance, low-latency block storage** for use with Compute instances. It functions like an external hard drive and supports:  
✔ **Dynamic Volume Scaling** – Resize volumes without downtime.  
✔ **Multi-Attach Mode** – Attach a volume to multiple instances.  
✔ **Encryption & Backups** – Built-in security and automatic snapshots. 

# **3. File Storage (Shared File System - NFS)**  

## **Overview:**  
OCI File Storage provides **a highly durable, scalable network file system (NFS)** that supports shared access across multiple Compute instances.  

## **Key Features:**  
✔ **Auto-Scaling** – No capacity limits, scales as data grows.  
✔ **High Availability** – Replicated across availability domains.  
✔ **Multi-Attach** – Can be mounted on multiple VMs simultaneously.  

---

## **2. Configuration Steps for OCI Storage Services**

### **2.1 Object Storage**

#### **Using OCI Console**

**Creating a Bucket:**

1. **Access the OCI Console**: Log in to your OCI account.
2. **Navigate to Object Storage**:
   - Open the navigation menu.
   - Select "Storage" and then "Object Storage."
3. **Create a Bucket**:
   - Click on "Create Bucket."
   - Enter a unique name for the bucket.
   - Choose the desired **Storage Tier**:
     - **Standard**: For frequently accessed data.
     - **Archive**: For infrequently accessed data.
   - Configure additional settings as needed.
   - Click "Create."

**Uploading Objects:**

1. Open the newly created bucket.
2. Click on "Upload."
3. Select files from your local system.
4. Click "Upload" to transfer the files to OCI.

#### **Using OCI CLI**

**Creating a Bucket:**

1. Ensure the OCI CLI is installed and configured.
2. Run the following command:

   ```bash
   oci os bucket create --name <bucket-name> --compartment-id <compartment-ocid> --storage-tier <Standard|Archive>
   ```


**Uploading Objects:**


```bash
oci os object put --bucket-name <bucket-name> --file <file-path>
```


#### **Using Terraform**

**Define the Bucket Resource:**


```hcl
resource "oci_objectstorage_bucket" "example_bucket" {
  compartment_id = var.compartment_id
  name           = "example_bucket"
  storage_tier   = "Standard" # or "Archive"
}
```


**Uploading Objects:**

Terraform primarily manages infrastructure. For object uploads, use the OCI CLI or SDKs.

---

### **2.2 Block Volume**

#### **Using OCI Console**

**Creating a Block Volume:**

1. **Navigate to Block Volumes**:
   - Open the navigation menu.
   - Select "Storage" and then "Block Volumes."
2. **Create a Volume**:
   - Click on "Create Block Volume."
   - Enter a name and select the compartment.
   - Choose the **Availability Domain**.
   - Specify the **Size** and **Performance** level.
   - Click "Create."

**Attaching to an Instance:**

1. Navigate to the instance details page.
2. Click on "Attach Block Volume."
3. Select the volume and attachment type.
4. Click "Attach."
5. Configure the volume within the instance's OS (e.g., format and mount).

#### **Using OCI CLI**

**Creating a Block Volume:**


```bash
oci bv volume create --availability-domain <AD-name> --compartment-id <compartment-ocid> --display-name <volume-name> --size-in-gbs <size>
```


**Attaching to an Instance:**


```bash
oci compute volume-attachment attach --instance-id <instance-ocid> --volume-id <volume-ocid> --type <paravirtualized|iscsi>
```


#### **Using Terraform**

**Define the Block Volume Resource:**


```hcl
resource "oci_core_volume" "example_volume" {
  availability_domain = var.availability_domain
  compartment_id      = var.compartment_id
  display_name        = "example_volume"
  size_in_gbs         = 50
}
```


**Attach the Volume to an Instance:**


```hcl
resource "oci_core_volume_attachment" "example_attachment" {
  instance_id = oci_core_instance.example_instance.id
  volume_id   = oci_core_volume.example_volume.id
  attachment_type = "paravirtualized"
}
```


---

## **2.3 File Storage (Continued)**  

### **Using OCI Console**  

#### **Creating a File System**  
1. **Navigate to File Storage**:  
   - Open the **navigation menu**.  
   - Select **Storage → File Storage**.  
2. **Create a File System**:  
   - Click **Create File System**.  
   - Enter a **name** and select a **compartment**.  
   - Click **Create**.  

#### **Creating a Mount Target**  
A **Mount Target** is required to access the file system.  
1. Navigate to **File Storage → Mount Targets**.  
2. Click **Create Mount Target**.  
3. Enter a **name**, select a **subnet**, and configure access.  
4. Click **Create**.  

#### **Mounting the File System on a Compute Instance**  
1. **Find the Mount Command**:  
   - Go to **File System Details**.  
   - Copy the **Mount Command**.  
2. **Run the Mount Command** on the Compute Instance:  
   ```sh
   sudo mount -t nfs <mount_target_IP>:/<filesystem_id> /mnt/myfolder
   ```  
3. **Verify the Mount**:  
   ```sh
   df -h
   ```  

---

### **Using OCI CLI**  

#### **Creating a File System**  
```sh
oci fs file-system create --compartment-id <compartment_ocid>
```  

#### **Creating a Mount Target**  
```sh
oci fs mount-target create --compartment-id <compartment_ocid> --subnet-id <subnet_ocid>
```  

#### **Mounting the File System on a Compute Instance**  
```sh
sudo mount -t nfs <mount_target_IP>:/<filesystem_id> /mnt/myfolder
```  

---

### **Using Terraform**  

#### **Defining the File Storage System**  
```hcl
resource "oci_file_storage_file_system" "example_fs" {
  compartment_id = var.compartment_id
}
```  

#### **Defining the Mount Target**  
```hcl
resource "oci_file_storage_mount_target" "example_mt" {
  compartment_id = var.compartment_id
  subnet_id      = var.subnet_id
}
```  

---

## **3. Backup Process (Manual & Automated)**  

### **3.1 Backup for Object Storage**  

#### **Manual Backup**  
1. **Use the OCI Console**:  
   - Navigate to **Object Storage** → **Select a bucket**.  
   - Click **Download** for the required objects.  

2. **Using OCI CLI**  
   ```sh
   oci os object get --bucket-name my-bucket --name backup.tar.gz --file backup.tar.gz
   ```  

3. **Using SDKs (Python Example)**  
   ```python
   import oci
   client = oci.object_storage.ObjectStorageClient(config)
   client.get_object("namespace", "my-bucket", "backup.tar.gz")
   ```  

---

#### **Automated Backup (Lifecycle Policies)**  

1. **Enable Object Lifecycle Policy**:  
   - Navigate to **Object Storage → Lifecycle Policies**.  
   - Click **Create Rule**.  
   - Set conditions (e.g., move objects to Archive Storage after 30 days).  
   - Click **Create**.  

2. **Using OCI CLI to Set Policy**  
   ```sh
   oci os bucket update --name my-bucket --namespace my-namespace --lifecycle-policy file://lifecycle_policy.json
   ```  
   Example `lifecycle_policy.json`:  
   ```json
   {
     "rules": [
       {
         "name": "move_to_archive",
         "action": "ARCHIVE",
         "objectNameFilter": {
           "inclusionPatterns": ["*.log"]
         },
         "timeAmount": 30,
         "timeUnit": "DAYS"
       }
     ]
   }
   ```  

---

### **3.2 Backup for Block Volume**  

#### **Manual Backup**  
1. **Navigate to OCI Console** → **Block Volumes**.  
2. Select a **Volume** → Click **Create Backup**.  
3. Choose **Backup Type** (Full or Incremental).  
4. Click **Create**.  

#### **Automated Backup Policy**  
1. **Navigate to**: **Block Storage → Backup Policies**.  
2. Click **Create Backup Policy**.  
3. Set **Schedule (Daily/Weekly/Monthly)**.  
4. Assign the **Policy to Block Volumes**.  

#### **Using OCI CLI for Automated Backup**  
```sh
oci bv backup-policy create --name "DailyBackupPolicy" --backup-schedule '[{"period": "DAILY"}]'
```  

---

### **3.3 Backup for File Storage**  

#### **Manual Snapshot**  
1. **Navigate to**: **File Storage → File Systems**.  
2. Select the file system → Click **Snapshots**.  
3. Click **Create Snapshot**.  

#### **Using OCI CLI for Snapshot**  
```sh
oci fs snapshot create --file-system-id <filesystem_ocid> --name "daily_backup"
```  

---

## **4. Real-Time Storage Synchronization to a Disaster Recovery (DR) System**  

### **4.1 Object Storage Sync**  
#### **Using OCI Object Replication**  
1. **Enable Replication**:  
   - Go to **Object Storage → Select Bucket**.  
   - Click **Enable Replication**.  
   - Select a **Destination Region**.  
   - Click **Enable**.  

#### **Using CLI for Sync**  
```sh
oci os object sync --src-bucket source-bucket --dest-bucket dest-bucket --namespace source-namespace
```

---

### **4.2 Block Volume Replication**  
#### **Using Console**  
1. **Navigate to Block Volumes** → **Select Volume**.  
2. Click **Enable Replication**.  
3. Choose **Destination Region** and **Compartment**.  
4. Click **Enable**.  

#### **Using OCI CLI for Replication**  
```sh
oci bv volume create --availability-domain <AD-name> --compartment-id <compartment-ocid> --source-volume-id <source-volume-ocid>
```  

---

### **4.3 File Storage Sync (Cross-Region Replication)**  
#### **Using Console**  
1. **Navigate to File Storage** → **Select File System**.  
2. Click **Enable Replication**.  
3. Select **Destination Region** and **Target File System**.  
4. Click **Enable**.  

#### **Using CLI for File Storage Sync**  
```sh
oci fs replication create --source-id <source-fs-ocid> --target-id <destination-fs-ocid>
```  

---

## **Conclusion**  
Oracle Cloud Infrastructure (OCI) provides **robust, scalable, and cost-effective storage solutions** across Object Storage, Block Volume, and File Storage. By implementing **best practices in backup, replication, and storage lifecycle management**, organizations can ensure **high availability and disaster recovery preparedness**.
