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
