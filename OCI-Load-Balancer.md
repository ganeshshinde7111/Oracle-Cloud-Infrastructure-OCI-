# PART: 1

# **Complete Guide to Load Balancers in Oracle Cloud Infrastructure (OCI)**  

Oracle Cloud Infrastructure (OCI) provides multiple **Load Balancer** options to distribute network traffic efficiently. Below is a **detailed explanation** of all types, along with **configuration steps**.

---

## **Types of Load Balancers in OCI**
1. **Network Load Balancer (NLB)**
2. **Application Load Balancer (ALB)**
3. **Public Load Balancer**
4. **Private Load Balancer**
5. **Flexible Load Balancer**  

Each type serves different use cases, based on performance, security, and traffic routing requirements.

---

# **1. Network Load Balancer (NLB)**
✅ **Works at Layer 4 (Transport Layer - TCP/UDP)**  
✅ **Provides ultra-low latency and high-performance traffic distribution**  
✅ **Preserves the original client IP**  

### **Key Features**
- Supports **TCP and UDP protocols**.
- Directly routes traffic to backend servers.
- **Fast & lightweight** (No SSL termination, session persistence, or advanced traffic inspection).
- **Preserves client IP address** (Backend servers see original client IP).
- Supports both **public and private deployments**.

### **Configuration Steps for NLB in OCI**
#### **Step 1: Create a Network Load Balancer**
1. Sign in to the **OCI Console**.
2. Navigate to **Networking > Load Balancers**.
3. Click **Create Load Balancer**.
4. Select **Network Load Balancer**.
5. Choose:
   - **Public or Private** (depending on requirement).
   - **Compartment** and **Virtual Cloud Network (VCN)**.
   - **Subnet** (Ensure it's regional or in the required availability domain).
6. Click **Next**.

#### **Step 2: Configure Backend Set**
1. Click **Add Backend Set**.
2. Provide a **Name**.
3. Select a **Load Balancing Policy** (e.g., Round Robin, IP Hash, Least Connections).
4. Configure **Health Check Policy**:
   - **Protocol:** TCP/HTTP
   - **Port:** Choose the backend service port
   - **Interval & timeout settings**.
5. Click **Create**.

#### **Step 3: Add Backend Servers**
1. Select the Backend Set created.
2. Click **Add Backends**.
3. Choose:
   - **Instance** or **IP Address**.
   - **Port** (Application Port, e.g., 80 for HTTP, 443 for HTTPS).
4. Click **Add**.

#### **Step 4: Configure Listeners**
1. Click **Listeners > Add Listener**.
2. Select **Protocol (TCP/UDP)**.
3. Choose **Port (e.g., 80, 443, 3306 for MySQL, etc.)**.
4. Click **Create**.

#### **Step 5: Review & Create**
- Review all settings.
- Click **Create Load Balancer**.
- Wait for **Active** status.
- Use **the Public or Private IP** assigned to test.

---

# **2. Application Load Balancer (ALB)**
✅ **Works at Layer 7 (Application Layer - HTTP/HTTPS)**  
✅ **Provides intelligent routing based on content (URLs, headers, cookies, etc.)**  
✅ **Supports SSL termination, session persistence, and WebSockets**  

### **Key Features**
- Supports **HTTP/HTTPS and WebSockets**.
- Can perform **SSL termination and re-encryption**.
- **Content-based routing** (Route requests based on URL, headers, or cookies).
- Supports **session persistence**.
- Can handle **public and private traffic**.

### **Configuration Steps for ALB in OCI**
#### **Step 1: Create an Application Load Balancer**
1. Go to **Networking > Load Balancers**.
2. Click **Create Load Balancer**.
3. Select **Application Load Balancer**.
4. Choose:
   - **Public or Private**.
   - **Compartment** and **VCN**.
   - **Subnet** (Regional or AD-specific).
5. Click **Next**.

#### **Step 2: Configure Backend Set**
1. Click **Add Backend Set**.
2. Provide a **Name**.
3. Select a **Load Balancing Policy**.
4. Configure **Health Check Policy**:
   - **Protocol:** HTTP/HTTPS.
   - **Port:** Backend application port.
   - **Path:** Enter **/health** or other health-check endpoint.
5. Click **Create**.

#### **Step 3: Add Backend Servers**
1. Select the Backend Set.
2. Click **Add Backends**.
3. Choose:
   - **Instance** or **IP Address**.
   - **Port** (E.g., 80 for HTTP, 443 for HTTPS).
4. Click **Add**.

#### **Step 4: Configure Listeners**
1. Click **Listeners > Add Listener**.
2. Choose **Protocol**:
   - **HTTP (Port 80) or HTTPS (Port 443)**.
3. (Optional) If using **SSL/TLS**, upload your **SSL certificate**.
4. Click **Create**.

#### **Step 5: Review & Create**
- Review settings.
- Click **Create Load Balancer**.
- Wait for **Active** status.
- Test using **Public or Private IP**.

---

# **3. Public Load Balancer**
✅ **Accessible from the internet**  
✅ **Used for websites, APIs, and public-facing apps**  

- **Supports both NLB and ALB**.
- Assigns a **public IP address**.
- Use **for external user access**.

### **Configuration**
- Follow **NLB or ALB steps**.
- Choose **Public** during **Load Balancer creation**.
- Ensure **proper security list rules** in VCN.

---

# **4. Private Load Balancer**
✅ **Only accessible within OCI VCN (internal use)**  
✅ **Used for private applications, internal microservices**  

- **Does not expose a public IP**.
- Use **for internal communication**.
- Requires **correct security group rules**.

### **Configuration**
- Follow **NLB or ALB steps**.
- Choose **Private** during **Load Balancer creation**.
- Ensure **backend instances can communicate**.

---

# **5. Flexible Load Balancer**
✅ **Can scale dynamically based on traffic needs**  
✅ **Combines features of Public and Private Load Balancers**  

### **Key Features**
- Can **auto-scale from 10 Mbps to 8 Gbps**.
- Supports **both public and private configurations**.
- Cost-efficient for **dynamic workloads**.

### **Configuration**
- During **Load Balancer creation**, select:
  - **Flexible**.
  - Choose **Bandwidth range (e.g., 10 Mbps to 8 Gbps)**.
- Other steps remain the same.

---

# **Conclusion: Choosing the Right Load Balancer**
| Feature | Network Load Balancer (NLB) | Application Load Balancer (ALB) | Public Load Balancer | Private Load Balancer | Flexible Load Balancer |
|---------|-----------------------------|---------------------------------|---------------------|---------------------|---------------------|
| **Layer** | Layer 4 (TCP/UDP) | Layer 7 (HTTP/HTTPS) | Can be NLB or ALB | Can be NLB or ALB | Can be NLB or ALB |
| **Traffic Type** | TCP, UDP | HTTP, HTTPS | Public internet traffic | Internal VCN traffic | Scalable traffic |
| **Use Case** | Real-time apps, VoIP, DBs | Web apps, APIs, SSL offloading | External users | Internal services | Dynamic workloads |
| **Client IP Preservation** | ✅ Yes | ❌ No | ✅ Yes | ✅ Yes | ✅ Yes |

# PART: 2

### **Network Load Balancer (NLB) vs. Application Load Balancer (ALB) in OCI**

Oracle Cloud Infrastructure (OCI) provides **two types of Load Balancers** to distribute incoming traffic efficiently:  

1. **Network Load Balancer (NLB)**  
2. **Application Load Balancer (ALB)**  

Both help manage traffic and improve availability, but they work at different levels and are suited for different use cases.  

---

## **1. Network Load Balancer (NLB)**
✅ **Works at Layer 4 (Transport Layer)**  
✅ **Best for high-performance, low-latency applications**  

### **How it Works**  
- NLB operates at the **TCP (Transmission Control Protocol) and UDP (User Datagram Protocol)** level.  
- It forwards incoming requests **directly to backend servers** without modifying the traffic.  
- Uses **IP-based routing** to distribute traffic efficiently.  

### **Key Features**
🔹 **Ultra-fast Performance:** Supports millions of requests per second.  
🔹 **Low Latency:** Ideal for real-time applications.  
🔹 **Supports TCP and UDP Traffic:** Useful for gaming, VoIP, and database replication.  
🔹 **Preserves Client IP Address:** Backend servers see the original client’s IP.  
🔹 **Health Checks:** Monitors backend servers to ensure availability.  

### **Use Cases**
- Load balancing for **databases, messaging services, and high-speed applications**.  
- Applications requiring **direct client IP visibility**.  
- Scenarios where **high throughput** and **low latency** are critical.  

---

## **2. Application Load Balancer (ALB)**
✅ **Works at Layer 7 (Application Layer)**  
✅ **Best for web applications and HTTP/HTTPS traffic**  

### **How it Works**  
- ALB inspects **HTTP and HTTPS traffic** and can make routing decisions based on content (e.g., URL, headers, cookies).  
- It can modify requests before forwarding them to backend servers.  
- Supports **advanced traffic management**, such as SSL termination and URL-based routing.  

### **Key Features**
🔹 **Content-Based Routing:** Routes traffic based on URL, headers, or cookies.  
🔹 **SSL Termination:** Decrypts SSL/TLS traffic to reduce backend server load.  
🔹 **Session Persistence:** Ensures that a user’s requests go to the same backend server.  
🔹 **Supports HTTP/HTTPS & WebSockets:** Ideal for web applications.  
🔹 **Health Checks:** Automatically detects and removes unhealthy backend servers.  

### **Use Cases**
- Hosting **websites, APIs, and microservices**.  
- Routing traffic based on **URL paths (e.g., /api vs. /images)**.  
- Managing **secure HTTPS traffic** with SSL offloading.  

---

## **Comparison Table: NLB vs. ALB**

| Feature | Network Load Balancer (NLB) | Application Load Balancer (ALB) |
|---------|-----------------------------|---------------------------------|
| **Layer** | Layer 4 (Transport) | Layer 7 (Application) |
| **Traffic Type** | TCP, UDP | HTTP, HTTPS |
| **Routing** | IP-based | URL, headers, cookies |
| **Performance** | High-speed, low latency | More processing due to content inspection |
| **SSL Termination** | ❌ No | ✅ Yes |
| **Session Persistence** | ❌ No | ✅ Yes |
| **Use Case** | Databases, messaging, real-time apps | Websites, APIs, microservices |

---

## **Which Load Balancer Should You Use?**
- ✅ Use **NLB** if you need **high-speed** and **low-latency** traffic routing, especially for **non-HTTP traffic (like databases, gaming, VoIP, or messaging applications).**  
- ✅ Use **ALB** if you are running **web applications, APIs, or services that require content-based routing (like HTTP and HTTPS traffic).**  

# PART: 3

# **Detailed Guide on Load Balancing Policies in OCI**  

Load Balancing Policies define how **incoming traffic is distributed** among backend servers in Oracle Cloud Infrastructure (OCI). These policies ensure **efficient utilization of backend resources** and improve performance.

## **Types of Load Balancing Policies in OCI**
OCI supports the following **three Load Balancing Policies**:  

1. **Round Robin**  
2. **IP Hash**  
3. **Least Connections**  

Each policy has a different mechanism for distributing traffic, and selecting the right one depends on your application's requirements.

---

## **1. Round Robin**
✅ **Distributes traffic evenly among all backend servers**  
✅ **Best for environments with equal server capacity**  
✅ **Simple and effective for balancing requests**  

### **How It Works**
- The Load Balancer **forwards each new request** to the **next server in line**.
- Once the last server in the list is reached, it starts over from the first server.  
- Each backend receives an **equal number of requests** over time.  

### **Example**
| Incoming Requests | Assigned Server |
|------------------|----------------|
| Request 1       | Server A        |
| Request 2       | Server B        |
| Request 3       | Server C        |
| Request 4       | Server A        |
| Request 5       | Server B        |
| Request 6       | Server C        |

### **Best Use Cases**
- Applications where **all backend servers have similar computing power**.  
- Suitable for **stateless applications** (e.g., API servers, microservices).  
- When **no session persistence is required**.

### **When to Avoid?**
- If some backend servers have **higher processing power** than others.
- When **session persistence** (sticky sessions) is needed.

---

## **2. IP Hash**
✅ **Ensures the same client is always directed to the same backend server**  
✅ **Best for applications requiring session persistence (sticky sessions)**  
✅ **Good for user authentication and personalized services**  

### **How It Works**
- Uses a **hashing algorithm** to map the **client's IP address** to a specific backend server.  
- Every request from a **particular IP address** is sent to the **same backend server** unless it goes down.  

### **Example**
| Client IP Address | Assigned Server |
|------------------|----------------|
| 192.168.1.1     | Server A        |
| 192.168.1.2     | Server B        |
| 192.168.1.3     | Server C        |
| 192.168.1.1     | Server A (again) |

### **Best Use Cases**
- Applications that require **session persistence**, such as:
  - **E-commerce websites** (where user carts must remain persistent).
  - **Banking applications** (users should remain on the same backend).
  - **Chat or messaging applications** (consistent server connection needed).
- Ensures **better user experience** by preventing session loss.

### **When to Avoid?**
- If backend servers are **not equally powerful**, traffic distribution may become uneven.
- When the number of users is very large, some servers may become overloaded.

---

## **3. Least Connections**
✅ **Sends traffic to the server with the fewest active connections**  
✅ **Best for handling uneven traffic loads efficiently**  
✅ **Ensures better resource utilization**  

### **How It Works**
- The Load Balancer **monitors active connections** for each backend server.  
- **New requests are sent to the server with the fewest active connections**.  
- Helps in maintaining **even workload distribution**.

### **Example**
| Server | Active Connections | New Request Assigned? |
|--------|--------------------|----------------------|
| Server A | 5                | ❌ No               |
| Server B | 2                | ✅ Yes              |
| Server C | 3                | ❌ No               |

Since **Server B has the fewest active connections**, it receives the next request.

### **Best Use Cases**
- **Applications with long-lived connections**, such as:
  - **Database servers**.
  - **Streaming services** (where users maintain continuous connections).
  - **Remote desktop applications**.
- **Backend servers with different computing capacities** (ensures higher-capacity servers handle more requests).

### **When to Avoid?**
- If all requests are **short-lived**, Round Robin may be more effective.
- Can be **less effective for highly dynamic environments**.

---

## **Comparison of Load Balancing Policies**

| Policy | Mechanism | Best For | Not Suitable For |
|--------|----------|----------|-----------------|
| **Round Robin** | Assigns requests sequentially to each backend server | Stateless applications, API servers, Microservices | Applications needing session persistence |
| **IP Hash** | Routes requests from the same IP to the same backend | E-commerce, Banking, Chat Apps | High-load applications with imbalanced backend servers |
| **Least Connections** | Routes requests to the backend server with the fewest active connections | Streaming, Database Servers, Remote Desktops | Stateless applications with short-lived requests |

---

## **How to Configure Load Balancing Policy in OCI?**
### **Step 1: Create a Load Balancer**
1. Log in to **OCI Console**.
2. Go to **Networking > Load Balancers**.
3. Click **Create Load Balancer**.
4. Choose **Network Load Balancer (NLB) or Application Load Balancer (ALB)**.
5. Select **Public or Private**.

### **Step 2: Configure Backend Set**
1. Click **Add Backend Set**.
2. Provide a **Name**.
3. **Choose a Load Balancing Policy**:
   - Round Robin
   - IP Hash
   - Least Connections
4. Set up **Health Check Policy**.
5. Click **Create**.

### **Step 3: Add Backend Servers**
1. Select the **Backend Set** created.
2. Click **Add Backends**.
3. Choose:
   - **Instance** or **IP Address**.
   - **Port** (E.g., 80 for HTTP, 443 for HTTPS).
4. Click **Add**.

### **Step 4: Configure Listener**
1. Click **Listeners > Add Listener**.
2. Select **Protocol** (HTTP, HTTPS, TCP, UDP).
3. Click **Create**.

### **Step 5: Review & Create**
- Review all settings.
- Click **Create Load Balancer**.
- Wait until it becomes **Active**.
- Test using the **Load Balancer IP or Domain**.

---

## **Conclusion: Which Policy Should You Use?**
- ✅ Use **Round Robin** for **equal distribution** when all backend servers are similar.  
- ✅ Use **IP Hash** for **session persistence** (e.g., Banking, E-commerce, Chat).  
- ✅ Use **Least Connections** when **backend servers have different capacities** or **long-lived connections**.  
