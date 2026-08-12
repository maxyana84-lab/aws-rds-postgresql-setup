# AWS RDS PostgreSQL Deployment & Network Troubleshooting

A hands-on DevOps project demonstrating the infrastructure deployment, multi-AZ networking configuration, and systematic connectivity troubleshooting for a publicly accessible **Amazon RDS PostgreSQL** instance within a custom AWS VPC.

---

**```## 📐 Architecture Overview**

**```[ Client (pgAdmin 4 / psql) ]**
             │
           **```  │ TCP Port 5432 (SSL/TLS Required)**
             ▼
     **```[ Internet Gateway ]**
             │
             ▼
      **```[ VPC Route Table ] (0.0.0.0/0 -> igw-xxxx)**
             │
             ▼
    **```[ Public Subnet Group ] (us-east-1a / us-east-1b)**
             │
             ▼
   **```[ Security Group (sg-xxxx) ]**
  **```  └─ Inbound Rule: PostgreSQL (5432) from Allowed IP /32**
             │
             ▼
   **```[ Amazon RDS PostgreSQL Instance ]


### Tech Stack
- **Cloud Infrastructure:** Amazon Web Services (AWS)
- **Database Engine:** PostgreSQL 16 (Amazon RDS)
- **Networking:** AWS VPC, Internet Gateway (IGW), DB Subnet Groups, Route Tables
- **Security:** Security Groups, Network ACLs, SSL/TLS Encryption
- **Tools Used:** pgAdmin 4, PowerShell, Git

---

## 🛠️ Step-by-Step Configuration

### 1. Networking Infrastructure
* Provisioned an Amazon VPC across multiple Availability Zones.
* Configured public DB Subnet Groups (public-subnet-group-v2).
* Attached an **Internet Gateway (IGW)** to route external traffic via Custom Route Tables (0.0.0.0/0 -> igw).

### 2. RDS Provisioning & Security
* Provisioned a PostgreSQL instance on Amazon RDS (db.t3.micro).
* Enabled **Public Accessibility** for remote client access.
* Attached a Security Group with restricted inbound rules allowing TCP traffic on port 5432.

---

## 🔍 Troubleshooting Connection Timeout Issue

During initial setup, external connection attempts from pgAdmin 4 timed out. A systematic diagnostic approach was used:

1. **Security Group Verification:** Identified that default security group rules restricted access to internal resources. Updated rules to allow the client IP address.
2. **VPC Route & Subnet Alignment:** Resolved VPC association mismatches where the RDS instance resided in a subnet lacking Internet Gateway routes. Re-provisioned the database instance within the fully routed public subnet group.
3. **Transport Layer Diagnostic:** Executed network layer validation in PowerShell to confirm open ports:
   `Test-NetConnection -ComputerName <RDS_ENDPOINT> -Port 5432` -> Result: `TcpTestSucceeded: True`
4. **Protocol / SSL Layer:** Configured `SSL mode = Require` in pgAdmin connection properties to pass PostgreSQL authentication requirements.

---

## 🛡️ Security Best Practices Applied

* **Least Privilege:** Access restricted specifically to client /32 IP CIDR blocks instead of unrestricted 0.0.0.0/0 in production.
* **Encryption in Transit:** Enforced SSL/TLS connections for all database clients.
