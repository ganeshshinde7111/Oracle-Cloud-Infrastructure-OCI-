# PART: 1
Here’s a **comprehensive list of all commonly used ports** along with their **port numbers, uses, and short descriptions**.  

---

### **1. Well-Known Ports (0 – 1023)**  
These ports are assigned by **IANA (Internet Assigned Numbers Authority)** and are used by common services.  

| **Port** | **Protocol** | **Usage** | **Description** |
|---------|------------|----------|----------------|
| 20 | FTP Data | File Transfer | Used for file transfer (Data Channel) |
| 21 | FTP Control | File Transfer | Used for control commands (Control Channel) |
| 22 | SSH | Secure Remote Login | Secure remote access and file transfer (SFTP) |
| 23 | Telnet | Unsecured Remote Login | Used for remote terminal access (Unsecure) |
| 25 | SMTP | Email Sending | Simple Mail Transfer Protocol for sending emails |
| 53 | DNS | Name Resolution | Resolves domain names to IP addresses |
| 67 | DHCP | IP Assignment | Assigns dynamic IP addresses (Server) |
| 68 | DHCP | IP Assignment | Assigns dynamic IP addresses (Client) |
| 69 | TFTP | Trivial File Transfer | Simple, lightweight file transfer protocol |
| 80 | HTTP | Web Browsing | Standard web traffic over the internet |
| 88 | Kerberos | Authentication | Network authentication protocol |
| 110 | POP3 | Email Retrieval | Retrieves email from a server |
| 119 | NNTP | Usenet News | Used for newsgroup services |
| 123 | NTP | Time Synchronization | Synchronizes clocks over the internet |
| 137-139 | NetBIOS | Windows Networking | Name resolution, file & printer sharing |
| 143 | IMAP | Email Retrieval | Retrieves and manages emails from a server |
| 161-162 | SNMP | Network Monitoring | Monitors and manages network devices |
| 179 | BGP | Routing Protocol | Border Gateway Protocol for internet routing |
| 194 | IRC | Chat | Internet Relay Chat for real-time communication |
| 389 | LDAP | Directory Services | Used for managing user directories |
| 443 | HTTPS | Secure Web Browsing | Secure HTTP traffic using SSL/TLS |
| 465 | SMTPS | Secure Email Sending | Encrypted email transmission |
| 514 | Syslog | System Logging | Centralized system logging protocol |
| 636 | LDAPS | Secure Directory Services | Secure LDAP communication |
| 873 | Rsync | File Synchronization | Remote file synchronization service |
| 989-990 | FTPS | Secure File Transfer | FTP over SSL/TLS |
| 993 | IMAPS | Secure Email Retrieval | Secure IMAP email access |
| 995 | POP3S | Secure Email Retrieval | Secure POP3 email access |

---

### **2. Registered Ports (1024 – 49151)**  
These ports are used for specific applications, cloud services, and DevOps tools.  

| **Port** | **Protocol** | **Usage** | **Description** |
|---------|------------|----------|----------------|
| 1025-5000 | Dynamic Ports | API & Web Services | Temporary connections for web applications |
| 1433 | MSSQL | Microsoft SQL Server | Used by Microsoft SQL Server databases |
| 1521 | Oracle DB | Oracle Database | Oracle database connectivity |
| 2049 | NFS | Network File System | File sharing over networks |
| 2181 | ZooKeeper | Distributed Coordination | Manages cluster configurations (Kafka, Hadoop) |
| 2375-2376 | Docker | Container Management | Docker daemon API (Unsecured/Secured) |
| 3306 | MySQL | Database | MySQL database connections |
| 3389 | RDP | Remote Desktop | Remote Desktop Protocol for Windows |
| 4040 | Spark UI | Apache Spark | Monitoring UI for Spark clusters |
| 5000 | Docker Registry | Private Docker Images | Stores and manages Docker container images |
| 5001 | NAS Web Server | Synology | Used for alternative NAS web services |
| 5432 | PostgreSQL | Database | PostgreSQL database connectivity |
| 5601 | Kibana | Log Visualization | Visualizing logs in ELK stack |
| 5900 | VNC | Remote Desktop | Virtual Network Computing remote desktop |
| 6379 | Redis | Caching | In-memory key-value store for caching |
| 6443 | Kubernetes API | Cluster Management | Kubernetes API server port |
| 7077 | Spark Master | Big Data Processing | Apache Spark cluster master communication |
| 8080 | Jenkins | CI/CD Automation | Used by Jenkins and Tomcat applications |
| 8081 | Nexus Repository | DevOps Repository | Sonatype Nexus repository manager |
| 8443 | HTTPS | Secure Web Apps | Alternative HTTPS for secure applications |
| 8888 | Jupyter Notebook | Data Science | Interactive Python notebooks for ML & AI |
| 9000 | SonarQube | Code Quality | Analyzing and improving code quality |
| 9090 | Prometheus | Monitoring | Monitoring & alerting for infrastructure |
| 9092 | Kafka | Message Broker | Apache Kafka for real-time data streaming |
| 9200 | Elasticsearch | Search Engine | Distributed search and analytics engine |
| 9418 | Git | Version Control | Git protocol for repositories |
| 9999 | Apache JMeter | Performance Testing | Used for performance and load testing |

---

### **3. Dynamic and Private Ports (49152 – 65535)**  
These ports are dynamically allocated for **cloud, VPNs, and temporary connections**.  

| **Port** | **Protocol** | **Usage** | **Description** |
|---------|------------|----------|----------------|
| 500 | ISAKMP | VPN Security | VPN security associations (IPSec) |
| 4500 | NAT-T | NAT Traversal | Secure VPN communication |
| 1194 | OpenVPN | Secure VPN | Open-source VPN protocol |
| 1812-1813 | RADIUS | Authentication | Remote Authentication Dial-In User Service |
| 3128 | Squid Proxy | Web Proxy | Proxy server for caching web traffic |
| 4789 | VXLAN | Overlay Networking | Virtualized network encapsulation |
| 50000 | Jenkins Agent | CI/CD Automation | Jenkins agent to master communication |
| 24224 | Fluentd | Log Processing | Collecting and forwarding logs |
| 1514 | Syslog TLS | Secure Logging | Encrypted system log transmission |

---

### **4. Cloud & DevOps Specific Ports**  
| **Port** | **Service** | **Usage** |  
|---------|------------|----------|  
| 10250 | Kubelet API | Kubernetes Node Management |  
| 10251 | Kube-Scheduler | Kubernetes Scheduling |  
| 10252 | Kube-Controller | Kubernetes Management |  
| 30443 | Istio Ingress | Kubernetes Service Mesh |  

---

### **Summary**
- **0-1023** → **Well-Known Ports** (Common services like HTTP, FTP, SSH, DNS, SMTP, etc.)  
- **1024-49151** → **Registered Ports** (Cloud, DevOps, Databases, Monitoring, CI/CD)  
- **49152-65535** → **Dynamic/Private Ports** (Cloud applications, VPNs, Custom APIs)  


# PART: 2

Here’s a **filtered list of all commonly used ports** categorized based on **Cloud, DevOps, Databases, Networking, Security, Monitoring, VPNs, and more**.

---

## **1. Web & HTTP-Based Services**
| **Port** | **Protocol** | **Usage** |
|---------|------------|----------|
| 80  | HTTP | Standard web traffic (Unsecured) |
| 443 | HTTPS | Secure web traffic (SSL/TLS) |
| 8080 | HTTP-Alt | Web applications (Jenkins, Tomcat) |
| 8443 | HTTPS-Alt | Secure web applications |
| 8888 | Jupyter | Jupyter Notebook for Python/AI |

---

## **2. Cloud & DevOps Services**
| **Port** | **Service** | **Usage** |
|---------|------------|----------|
| 2375-2376 | Docker API | Docker container management |
| 5000 | Docker Registry | Private container registry |
| 6443 | Kubernetes API | Kubernetes cluster management |
| 10250 | Kubelet API | Kubernetes node communication |
| 10251 | Kube-Scheduler | Kubernetes scheduling service |
| 10252 | Kube-Controller | Kubernetes management |
| 30443 | Istio Ingress | Kubernetes service mesh |

---

## **3. CI/CD (Continuous Integration & Deployment)**
| **Port** | **Service** | **Usage** |
|---------|------------|----------|
| 8080 | Jenkins | CI/CD automation |
| 50000 | Jenkins Agent | Jenkins master-agent communication |
| 8081 | Nexus Repository | Artifact management |
| 9000 | SonarQube | Code quality analysis |

---

## **4. Databases**
| **Port** | **Database** | **Usage** |
|---------|------------|----------|
| 1433 | Microsoft SQL Server | MS SQL database connections |
| 1521 | Oracle DB | Oracle database service |
| 3306 | MySQL | MySQL database connections |
| 5432 | PostgreSQL | PostgreSQL database connectivity |
| 6379 | Redis | In-memory caching database |
| 9200 | Elasticsearch | Distributed search engine |

---

## **5. Messaging & Streaming Services**
| **Port** | **Service** | **Usage** |
|---------|------------|----------|
| 5672 | RabbitMQ | Message queuing |
| 9092 | Kafka | Real-time data streaming |

---

## **6. Logging & Monitoring**
| **Port** | **Service** | **Usage** |
|---------|------------|----------|
| 514 | Syslog | System log service |
| 5601 | Kibana | Log visualization (ELK Stack) |
| 9090 | Prometheus | Infrastructure monitoring |
| 24224 | Fluentd | Log collection and forwarding |

---

## **7. VPN & Security Services**
| **Port** | **Service** | **Usage** |
|---------|------------|----------|
| 500 | ISAKMP | VPN security association (IPSec) |
| 4500 | NAT-T | VPN NAT traversal |
| 1194 | OpenVPN | Open-source VPN protocol |
| 1812-1813 | RADIUS | Authentication and authorization |

---

## **8. Networking & Infrastructure**
| **Port** | **Service** | **Usage** |
|---------|------------|----------|
| 22  | SSH | Secure remote login |
| 23  | Telnet | Unsecured remote login |
| 53  | DNS | Domain name resolution |
| 67-68 | DHCP | IP address assignment |
| 179 | BGP | Internet routing protocol |

---

## **9. File Transfer & Sharing**
| **Port** | **Service** | **Usage** |
|---------|------------|----------|
| 20-21 | FTP | File transfer protocol |
| 69 | TFTP | Trivial file transfer protocol |
| 2049 | NFS | Network file system |

---

## **10. Remote Access & Desktop Services**
| **Port** | **Service** | **Usage** |
|---------|------------|----------|
| 3389 | RDP | Remote Desktop Protocol (Windows) |
| 5900 | VNC | Virtual Network Computing |

---

### **Summary**
- **Web Apps** → Ports **80, 443, 8080, 8443**  
- **Cloud & DevOps** → Ports **2375, 6443, 10250**  
- **CI/CD** → Ports **8080, 50000, 8081**  
- **Databases** → Ports **1433, 1521, 3306, 5432**  
- **Messaging** → Ports **5672, 9092**  
- **Logging & Monitoring** → Ports **514, 5601, 9090**  
- **VPN & Security** → Ports **500, 4500, 1194**  
- **Networking** → Ports **22, 23, 53, 67**  
- **File Transfer** → Ports **21, 69, 2049**  
- **Remote Access** → Ports **3389, 5900**  
