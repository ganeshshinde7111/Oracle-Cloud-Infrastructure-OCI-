# 🎓 **OCI Compute CPU Utilization Management – Study & Interview Guide**

---

## ✅ 1. **What is CPU Utilization Management in OCI?**

It involves **monitoring, analyzing, and optimizing** the CPU (OCPU) usage of compute instances to:

* Avoid **overprovisioning** (waste of resources and cost)
* Prevent **underprovisioning** (performance bottlenecks)
* Improve **cost-efficiency** and **system performance**
* Enable **scaling** and **automation** based on usage trends

---

## ✅ 2. **When to Take Action on CPU Utilization?**

| Condition                      | Action                                              |
| ------------------------------ | --------------------------------------------------- |
| CPU consistently < 30%         | Resize to a smaller shape to reduce cost            |
| Frequent CPU > 80% usage       | Scale up or enable autoscaling                      |
| CPU pegged at 100%             | Investigate application or consider a larger shape  |
| Dynamic, fluctuating workloads | Use instance pools with autoscaling                 |
| Scheduled low activity periods | Use lifecycle policies to stop/scale down instances |

---

## ✅ 3. **Where is CPU Monitoring & Management Done in OCI?**

### Via Console:

* **Compute → Instances → Monitoring Tab**
* **Monitoring → Metrics Explorer**
* **Monitoring → Alarms**
* **Compute → Instance Pools → Autoscaling**

### Via CLI:

#### A. View CPU Utilization:

```bash
oci monitoring metric-data summarize-metrics-data \
--namespace oci_computeagent \
--query-text "CpuUtilization[1h].mean()" \
--compartment-id <compartment_ocid> \
--resource-id <instance_ocid>
```

#### B. Resize Shape (Flex Instances):

```bash
oci compute instance update \
--instance-id <instance_ocid> \
--shape-config '{"ocpus": 2, "memoryInGBs": 8}'
```

---

## ✅ 4. **How to Optimize CPU Utilization in OCI**

### A. **Use Flexible Shapes**

* Use `VM.Standard3.Flex` or `VM.Standard.E4.Flex`
* Set custom OCPUs and memory based on need
* Avoid overpaying for underused resources

### B. **Set CPU Alarms**

* Example: Alert if `CpuUtilization[1m].mean() > 80`
* Notify via email, Slack, or trigger OCI Functions

### C. **Enable Autoscaling (Instance Pools)**

* Scale Out: when avg CPU > 70%
* Scale In: when avg CPU < 30%
* Requires instance configuration + pool + rules

### D. **Use Oracle Cloud Agent for In-depth Metrics**

* Enable agent:

  ```bash
  sudo systemctl enable --now oracle-cloud-agent
  ```
* Get metrics like:

  * Load averages
  * Steal time (indicates CPU contention)
  * System/user CPU breakdown

### E. **Analyze Historical CPU Trends**

* Use **Metrics Explorer**:

  * Namespace: `oci_computeagent`
  * Metric: `CpuUtilization`
  * Aggregation: mean, max, min
  * Group by instance or compartment

### F. **Set Lifecycle Policies (Non-Prod/Dev)**

* Auto-stop idle instances after hours
* Example CLI:

  ```bash
  oci compute instance action \
  --instance-id <ocid> --action STOP
  ```

---

## ✅ 5. **Best Practices**

| Area               | Recommendation                                                  |
| ------------------ | --------------------------------------------------------------- |
| **Shape Sizing**   | Use Flex shapes with only required OCPUs                        |
| **Monitoring**     | Set alarms for over/under-utilization                           |
| **Autoscaling**    | Use for workloads with fluctuating demand                       |
| **OS Checks**      | Use `top`, `htop`, `vmstat`, `pidstat`, `iostat`                |
| **Lifecycle Mgmt** | Stop unused Dev/Test instances outside working hours            |
| **Review Cycle**   | Monthly resource utilization audit and cost optimization review |

---

## 🧠 Real-Time Interview Questions (CPU Optimization Focus)

### General Questions:

1. What is CPU utilization in OCI and why is it important?
2. How can you monitor CPU usage of an OCI compute instance?
3. What tools in OCI help with analyzing CPU metrics?

### Hands-On / Use Case Questions:

4. Your OCI instance shows 90% CPU usage—what steps will you take?
5. How would you implement autoscaling based on CPU utilization?
6. What is the advantage of using a flexible shape for CPU tuning?
7. How can you automate CPU optimization tasks in OCI?
8. What’s the difference between vertical scaling and horizontal scaling in context of CPU?

### Advanced / Architecture-Level:

9. How would you design a scalable, CPU-efficient compute solution for variable workloads?
10. How does CPU steal time impact performance and how do you detect it in OCI?

---

---

# ✅ **Answers to Interview / Study Questions**

---

### ### ⚙️ General Questions

---

### **1. What is CPU utilization in OCI and why is it important?**

**Answer:**
CPU utilization measures how much of a compute instance’s allocated CPU (OCPUs) is actively being used. It’s important because:

* Low utilization = overprovisioned = wasted cost
* High utilization = underprovisioned = performance degradation
* Proper CPU utilization helps optimize cost, performance, and scalability

---

### **2. How can you monitor CPU usage of an OCI compute instance?**

**Answer:**

* **Console**: Go to *Monitoring > Metrics Explorer* or *Instance Monitoring Tab*
* **Metric**: `CpuUtilization`
* **Aggregation**: Use mean, max, or min over intervals
* **CLI**: Use `oci monitoring metric-data summarize-metrics-data` command
* **Alarms**: Set up thresholds to alert on high/low CPU usage

---

### **3. What tools in OCI help with analyzing CPU metrics?**

**Answer:**

* **Metrics Explorer**
* **Monitoring Alarms**
* **Oracle Cloud Agent (Compute Plugin)**
* **CLI Metrics Query**
* **OS Tools**: `top`, `htop`, `vmstat`, `iostat`, `sar`

---

### 🛠️ **Hands-On / Use Case Questions**

---

### **4. Your OCI instance shows 90% CPU usage—what steps will you take?**

**Answer:**

1. Confirm the spike is sustained (not just a burst)
2. Check the OS for top processes consuming CPU
3. Analyze application performance logs
4. Check for CPU steal time (if using shared host)
5. Consider:

   * Vertical scaling (increase OCPUs)
   * Moving to a more powerful shape
   * Horizontal scaling (add instances with load balancer)
6. Set up an alarm for future spikes

---

### **5. How would you implement autoscaling based on CPU utilization?**

**Answer:**

1. Create an **Instance Configuration**
2. Launch an **Instance Pool**
3. Attach **Autoscaling Configuration**

   * Scale-Out Rule: When average CPU > 70%
   * Scale-In Rule: When average CPU < 30%
4. Monitor scaling events via logs and alarms

---

### **6. What is the advantage of using a flexible shape for CPU tuning?**

**Answer:**

* Allows custom CPU (OCPU) and memory allocation
* Avoids overpaying for unused capacity
* Can scale vertically by updating shape config without full replacement
* Perfect for applications with unpredictable or seasonal loads

---

### **7. How can you automate CPU optimization tasks in OCI?**

**Answer:**

* **Alarms + Notifications + Functions**: Trigger automatic resize or action
* **Scheduled CLI Scripts**: Resize or stop/start instances based on CPU thresholds
* **Resource Scheduler**: Auto shutdown/start during off-hours
* **Terraform + OCI SDKs**: Automate instance resizing or pool scaling

---

### **8. What’s the difference between vertical scaling and horizontal scaling in context of CPU?**

**Answer:**

| Type                   | Description                                                                |
| ---------------------- | -------------------------------------------------------------------------- |
| **Vertical Scaling**   | Increase/decrease OCPUs of a single instance (e.g., from 2 to 4 OCPUs)     |
| **Horizontal Scaling** | Add or remove instances in a pool (e.g., add 2 more instances if CPU high) |

* **Vertical** is simple but has limits
* **Horizontal** is scalable and highly available

---

### 🧠 **Advanced / Architecture-Level Questions**

---

### **9. How would you design a scalable, CPU-efficient compute solution for variable workloads?**

**Answer:**

1. Use **Instance Configuration + Instance Pool**
2. Choose **VM.Standard3.Flex** or **E4.Flex** shapes
3. Configure **Autoscaling** on CPU thresholds
4. Place under **Load Balancer** (with health checks)
5. Enable **Monitoring Alarms** for proactive actions
6. Use **Tagging** for reporting and cost control
7. Optimize placement in **ADs for HA**

---

### **10. How does CPU steal time impact performance and how do you detect it in OCI?**

**Answer:**

* **CPU Steal Time** occurs when the hypervisor delays access to CPU due to contention (mainly on shared hosts)
* It leads to degraded performance even if your instance shows low usage

**How to Detect:**

* Check metrics from Oracle Cloud Agent or Linux tools like `vmstat`
* Look for `%steal` column
* High steal time = consider switching to dedicated host or bare metal

---

# 🧠 **OCI Instance Storage Utilization Management – Study Guide**

---

### ✅ **1. What is Storage Utilization Management in OCI?**

**Definition**:
Storage Utilization Management refers to **monitoring, analyzing, and optimizing how block volumes, boot volumes, and local NVMe disks** are used by OCI compute instances to ensure:

* Sufficient disk space for apps and OS
* Optimal IOPS and throughput performance
* Avoiding overprovisioning or wasted cost
* Maintaining high availability & durability

---

### 🕒 **2. When Should You Manage Storage Utilization?**

| Scenario                                    | Action                                              |
| ------------------------------------------- | --------------------------------------------------- |
| Disk usage > 80%                            | Resize volume or add additional storage             |
| Unused volume capacity                      | Downsize or backup and recreate                     |
| High I/O latency or low throughput          | Upgrade performance tier or switch to higher volume |
| Application errors due to "disk full"       | Investigate file system; extend volume if needed    |
| Change in workload (IO-intensive apps, DBs) | Resize volumes or attach additional volumes         |
| Scheduled scaling (e.g., batch processing)  | Pre-attach temp volumes or use auto-mount scripts   |

---

### 🌍 **3. Where to Manage Storage Utilization in OCI?**

#### 🔹 From OCI Console:

* **Compute → Instances → Attached Volumes**
* **Block Storage → Block Volumes / Boot Volumes**
* **Monitoring → Metrics Explorer** (for volume metrics)
* **Alarms → Set for VolumeUsedPercent, IOPS, Latency**

#### 🔹 From CLI:

* View volume stats:

  ```bash
  df -h          # Check disk space usage
  iostat -xm 2   # Monitor IOPS, throughput, latency
  ```

* Resize volume:

  ```bash
  oci bv volume update --volume-id <ocid> --size-in-gbs 100
  ```

* Extend partition (Linux):

  ```bash
  growpart /dev/sdb 1
  resize2fs /dev/sdb1
  ```

---

### ⚙️ **4. How to Manage and Optimize OCI Storage Usage**

---

#### 🔸 A. **Right-Size Block and Boot Volumes**

* Choose volume size and performance tier (Balanced / Higher Performance / Ultra High)
* Start small → monitor → resize if needed
* Use performance auto-tuning for dynamic optimization (default: enabled)

---

#### 🔸 B. **Monitor Storage Metrics**

Use **OCI Monitoring + Metrics Explorer** to track:

* `VolumeUsedPercent`
* `VolumeReadOps`, `VolumeWriteOps`
* `VolumeReadThroughput`, `VolumeWriteThroughput`
* `VolumeReadLatency`, `VolumeWriteLatency`

> Combine with **Cloud Agent plugins** for OS-level stats (disk usage, inodes, swap)

---

#### 🔸 C. **Use Alarms for Proactive Storage Alerts**

**Example**: Alert when disk usage > 85%

```sql
VolumeUsedPercent[5m].mean() > 85
```

Set up Notification topic (email/slack)

---

#### 🔸 D. **Extend Volume Without Downtime (Online Resize)**

* OCI allows **online volume resize**
* Steps:

  1. Resize volume via Console or CLI
  2. Grow partition: `growpart`
  3. Resize file system: `resize2fs`, `xfs_growfs` depending on FS

> No need to unmount or reboot in most cases

---

#### 🔸 E. **Optimize Disk Layout (Multi-Volume Architecture)**

For I/O intensive apps:

* Separate volumes for OS, App, DB, Logs
* Striping (RAID 0) for high throughput
* Mount with optimized options (`noatime`, `deadline` scheduler)

---

#### 🔸 F. **Use Backup and Lifecycle Policies**

* Regular **block volume backups** (manual or scheduled)
* **Auto-delete temp volumes** for dev/test workloads
* Use **volume groups** for consistent backup/snapshot of multi-volume setups

---

#### 🔸 G. **Track and Clean Up Orphaned Volumes**

* Unattached volumes = cost leakage
* Use tags + volume lifecycle scripts to audit unused volumes
* Delete or archive to object storage

---

### 📋 **Quick Summary Table**

| Category      | Best Practice                                                |
| ------------- | ------------------------------------------------------------ |
| Volume Sizing | Start small, monitor usage, resize when needed               |
| Monitoring    | Enable volume metrics + OS-level disk usage tracking         |
| Alerts        | Setup alarms for disk usage %, IOPS, throughput              |
| Resizing      | Use online resize with `growpart` + `resize2fs`              |
| Multi-volume  | Split OS / app / DB / logs for isolation and performance     |
| Backup        | Use scheduled backups, volume groups, and lifecycle policies |
| Cleanup       | Regularly identify and delete unused volumes                 |

---

---

# 🎯 **Real-Time Interview Questions + Answers (Storage Focused)**

---

### ✅ Q1: **How do you monitor storage usage for an OCI compute instance?**

**Answer**:
You can monitor storage usage on two levels:

1. **At OCI level**:

   * Use **OCI Metrics Explorer**:

     * Namespace: `oci_blockstore`
     * Metric: `VolumeUsedPercent`, `VolumeReadOps`, `VolumeWriteThroughput`, etc.

2. **At OS level**:

   * Use Linux commands like:

     ```bash
     df -h              # Disk space usage
     iostat -xm 2       # IOPS and throughput
     lsblk              # Block device info
     du -sh *           # Per-folder space usage
     ```

---

### ✅ Q2: **Can you resize an attached block volume in OCI without downtime?**

**Answer**:
Yes. OCI supports **online block volume resizing** without detaching or rebooting the instance. The steps are:

1. Resize volume from **Console** or via CLI:

   ```bash
   oci bv volume update --volume-id <ocid> --size-in-gbs <new_size>
   ```

2. Expand partition:

   ```bash
   sudo growpart /dev/sdb 1
   ```

3. Resize the file system:

   ```bash
   sudo resize2fs /dev/sdb1     # ext4
   sudo xfs_growfs /mountpoint  # xfs
   ```

---

### ✅ Q3: **What performance tuning options do you have for block volumes?**

**Answer**:

* OCI offers **Block Volume Performance Tiers**:

  * **Lower Cost** (50 IOPS/GB)
  * **Balanced** (60 IOPS/GB)
  * **Higher Performance** (75 IOPS/GB)
  * **Ultra High** (VPUs-based, highly tunable)

* You can change tiers dynamically:

  ```bash
  oci bv volume update --volume-id <ocid> --vpus-per-gb 20
  ```

* Use **Performance Auto-Tuning** (enabled by default) to adjust IOPS and throughput based on workload.

---

### ✅ Q4: **How do you handle low disk space alerts on OCI instances?**

**Answer**:

* Set up **OCI Monitoring alarms** on:

  * `VolumeUsedPercent > 85` for 5 minutes

* Enable **OS Agent plugins** to send alerts based on:

  * Disk usage (`df`)
  * Inodes
  * Swap usage

* Automate cleanup tasks:

  * Cron jobs to delete temp files
  * Rotate logs using `logrotate`

---

### ✅ Q5: **How can you ensure high performance and fault tolerance for database storage on OCI?**

**Answer**:

* Use **separate volumes** for:

  * Data
  * Logs
  * TempDB
  * Backups

* Choose **High Performance or Ultra High** performance tiers

* Use **Volume Groups** for snapshot consistency across DB components

* Implement **RAID (OS-level)** or use Oracle’s HA services like **OCI Database with ASM**

---

### ✅ Q6: **What is the difference between Block Volumes and Local NVMe in OCI?**

**Answer**:

| Feature     | Block Volume                      | Local NVMe                       |
| ----------- | --------------------------------- | -------------------------------- |
| Persistence | Persistent across reboots         | **Ephemeral** (lost after stop)  |
| Backup      | Snapshot and backup supported     | Not supported                    |
| Use Case    | General-purpose + high durability | Temp storage, caching, high IOPS |
| Performance | Tunable IOPS                      | Very high IOPS, low latency      |
| Durability  | Across availability domains       | Only tied to specific instance   |

---

### ✅ Q7: **How do you clean up unused storage in OCI to reduce cost?**

**Answer**:

* Use **tagging and reporting** to identify unattached volumes:

  ```bash
  oci bv volume list --compartment-id <ocid> --lifecycle-state AVAILABLE
  ```

* Automate cleanup using **scheduled jobs** or **Terraform scripts**

* Archive old data to **Object Storage (Standard/Infrequent Access/Archive)**

* Enable **block volume lifecycle policies** for auto-deletion

---

### ✅ Q8: **How do you ensure storage is available and performant during peak load periods?**

**Answer**:

* Use **performance auto-tuning** or manually assign higher VPUs

* Design with **striped volumes** or multiple volume mounts

* Pre-warm file systems before batch loads

* Use **Instance Pools + Pre-attached Volumes** for autoscaling workloads

---

## 🧠 Tip for Interviews

* Always **combine OCI Console methods + CLI knowledge + OS-level insight**
* Be prepared to **talk about real use cases**, like:

  * How you diagnosed an application crash due to full disk
  * How you scaled up volumes for a high-throughput workload
  * Cost-saving by identifying unused volumes via scripts

---

---

# 💾 **OCI Instance Memory Utilization Management — Study Guide**

---

### ✅ **1. What is Memory Utilization Management in OCI?**

Memory utilization management in OCI involves **monitoring, analyzing, and optimizing RAM usage** of compute instances to:

* Prevent **out-of-memory (OOM)** errors and crashes
* Avoid **over-provisioning** (excess unused memory = wasted cost)
* Enable **right-sizing** of instances
* Optimize **performance and cost** based on real usage patterns

---

### 🕒 **2. When Do You Need to Manage Memory Utilization?**

| Situation                                 | Action                                        |
| ----------------------------------------- | --------------------------------------------- |
| Memory usage consistently < 40%           | **Downsize** memory to reduce costs           |
| Usage spikes near 90–100%                 | Consider **scaling up** memory                |
| Application crashes with OOM errors       | **Investigate + Add Memory** or tune config   |
| Dynamic workload pattern                  | Use **flex shapes + autoscaling**             |
| Performance degradation without CPU spike | Check for **memory leaks or buffer overflow** |

---

### 🌍 **3. Where Do You Monitor and Manage Memory Usage?**

#### 🔹 **A. From OCI Console:**

* **Monitoring > Metrics Explorer**

  * Namespace: `oci_computeagent`
  * Metric: `MemoryUtilization`
  * Resource ID: Instance OCID

* **Monitoring > Alarms** for memory thresholds

#### 🔹 **B. From OS Level (Linux)**:

```bash
free -m                 # Free, used, cached memory
vmstat 2                # System memory usage
top / htop              # Live memory per process
ps aux --sort=-%mem     # High memory processes
```

#### 🔹 **C. CLI Example**:

```bash
oci monitoring metric-data summarize-metrics-data \
--namespace oci_computeagent \
--query-text "MemoryUtilization[1h].mean()" \
--resource-id <instance_ocid> \
--compartment-id <compartment_ocid>
```

---

### ⚙️ **4. How to Manage and Optimize Memory Utilization**

#### 🔸 A. **Use Flexible Shapes (VM.Standard3.Flex, etc.)**

* Adjust memory to match exact workload:

  ```bash
  oci compute instance update --instance-id <id> \
  --shape-config '{"ocpus": 2, "memoryInGBs": 8}'
  ```

* Resize **without stopping the instance** (on supported shapes)

---

#### 🔸 B. **Set Alarms for High Memory Usage**

* Example alarm query:

  ```
  MemoryUtilization[5m].mean() > 85
  ```

* Notification via email or Slack for DevOps teams

---

#### 🔸 C. **Enable OCI Cloud Agent Plugins for Memory Monitoring**

```bash
sudo systemctl enable --now oracle-cloud-agent
sudo oci-cloud-agent state
```

* MemoryUtilization metric only available if this plugin is active

---

#### 🔸 D. **Analyze Memory Trends in Metrics Explorer**

* Group by:

  * Avg, Max, or Min usage
  * Time intervals
  * Instance resource ID

* Helps identify usage trends and right-sizing opportunities

---

#### 🔸 E. **Perform Application-Level Tuning**

* Java: Tune heap size (`-Xmx`, `-Xms`)
* MySQL: Adjust buffer pool size
* Apache/Nginx: Control worker processes and buffer limits

---

#### 🔸 F. **Automate Cleanup & Optimize OS**

* Enable swap if needed:

  ```bash
  sudo swapon -s
  sudo dd if=/dev/zero of=/swapfile bs=1G count=2
  sudo mkswap /swapfile && sudo swapon /swapfile
  ```

* Schedule cleanup:

  * Clear system caches
  * Restart memory-heavy daemons periodically

---

## 🧠 **Real-Time Interview Questions + Answers (Memory Focus)**

---

### ✅ Q1: **How do you monitor memory usage on an OCI instance?**

**Answer**:

* Use **OCI Metrics Explorer** with:

  * Namespace: `oci_computeagent`
  * Metric: `MemoryUtilization`

* On OS level:

  ```bash
  free -m
  top / htop
  ps aux --sort=-%mem
  ```

---

### ✅ Q2: **What would you do if memory usage reaches 95% on a production instance?**

**Answer**:

1. Check running processes (`htop`, `ps aux`)
2. Analyze application for leaks or misconfiguration
3. Clear unused cache, enable swap if disabled
4. Resize instance with **more memory**
5. If flexible shape, update memory without recreating:

   ```bash
   oci compute instance update --memory-in-gbs <new_value>
   ```

---

### ✅ Q3: **How do flexible VM shapes help in memory optimization?**

**Answer**:

* Flexible shapes (e.g., `VM.Standard3.Flex`) allow **custom allocation of CPU + Memory**.
* You can start with minimal configuration and **scale RAM up or down** based on actual usage—no need to switch shape families.
* Helps reduce **unused resources = lower costs**.

---

### ✅ Q4: **Can you resize memory of a running OCI instance without stopping it?**

**Answer**:

* If using **Flexible shapes**, yes.
* Use:

  ```bash
  oci compute instance update --shape-config '{"memoryInGBs": <new_value>}' ...
  ```
* Ensure **Cloud Agent plugins** are enabled to get full metrics for resizing decisions.

---

### ✅ Q5: **What could cause high memory usage despite low CPU utilization?**

**Answer**:

* **Memory leaks** in applications
* **Buffer/cache bloat**
* **Background daemons** consuming RAM
* **Improper JVM settings**
* **OS-level caching not being released**

---

### ✅ Q6: **How do you prevent memory overuse in long-running apps?**

**Answer**:

* Implement **health checks** and **memory limits**
* Use **autoscaling** based on memory if part of instance pools
* Schedule **application restarts**
* Use **alerts + cleanup scripts**

---

## 📌 Best Practices Summary

| Category            | Practice                           |
| ------------------- | ---------------------------------- |
| **Monitoring**      | Use agent plugins + OS tools       |
| **Alerts**          | Set threshold alarms (e.g., >85%)  |
| **Scaling**         | Use flex shapes for RAM tuning     |
| **Analysis**        | Use Metrics Explorer for trends    |
| **Optimization**    | Tune app configs, cache, swap      |
| **Cost Efficiency** | Downsize when RAM is underutilized |

---
