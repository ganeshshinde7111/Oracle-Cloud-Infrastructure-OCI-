US-East Region
│
└── Sandbox Compartment
    │
    ├── VCN (Virtual Cloud Network) (10.0.0.0/16)
    │    ├── Subnet 1: Public (for Bastion)
    │    │        └── Internet Gateway (for Bastion)
    │    │
    │    └── Subnet 2: Private (for GPU VM) 10.0.2.0/24
    │             └── NAT Gateway (for GPU VM)
    │
    ├── Route Tables (for both subnets)
    │    ├── Subnet 1: Public (for Bastion)
    │    │         ├── Destination CIDR: 0.0.0.0/0
    │    │         ├── Target Type: Internet Gateway
    │    │         └── Target: Select Target Internet Gateway
    │    │    
    │    └── Subnet 2: Private (for GPU VM) 
    │              ├── Destination CIDR: 0.0.0.0/0
    │              ├── Target Type: NAT Gateway
    │              └── Target: sandbox-nat-gw
    │
    └── Security Lists (firewall rules)
         ├── Bastion: allow SSH from admin IP only
         │    ├── Ingress Rule:
         │    │    ├── Source CIDR: Your office/home IP (e.g., 49.32.110.45/32)
         │    │    ├── Protocol: TCP
         │    │    ├── Port: 22
         │    │    └── Description: “Allow SSH for admins”
         │    │
         │    └── Egress Rule:
         │         ├── Destination: 0.0.0.0/0
         │         ├── Protocol: All
         │         └── Description: “Allow all outbound”
         │    
         └── GPU VM: allow SSH only from Bastion subnet
              ├── Ingress Rule:
              │    ├── Source CIDR: 10.0.1.0/24 (public subnet range — bastion)
              │    ├── Protocol: TCP
              │    ├── Port: 22
              │    └── Description: “SSH only from Bastion subnet”
              │
              └── Egress Rule:
                   ├── Destination: 0.0.0.0/0
                   ├── Protocol: All
                   └── Description: “Allow all outbound”


ssh -i C:\Users\Lenovo\Downloads\ssh-key-2025-10-04.key -N -L 60022:10.0.2.202:22 -p 22 ocid1.bastionsession.oc1.ap-mumbai-1.amaaaaaayoo6xvaawxieqf3uqnaj22ec46dzhe4ziiv3gwh346ixyaxrif7a@host.bastion.ap-mumbai-1.oci.oraclecloud.com


ssh -i C:\Users\Lenovo\Downloads\ssh-key-2025-10-04 -p 22 opc@161.118.176.132
