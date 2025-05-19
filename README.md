# AWS_VPC_Pubic-Private
## 🎯 Goal
This project demonstrates how to securely set up an AWS Virtual Private Cloud(VPC) using public and private subnets
# 🛠️ AWS VPC Setup: Bastion Host, NAT Gateway, and Private EC2
### 🔐 Key Objectives:
- Create a **secure and logically separated** environment.
- Use **Bastion Host** in a public subnet for SSH access to the private EC2.
- Use a **NAT Gateway** to allow the private EC2 to access the internet.
- Proper configuration of **route tables** and **security groups**.

---

## 📦 AWS Resources to Be Created

| Resource         | Description                                   |
|------------------|-----------------------------------------------|
| VPC              | Custom VPC with CIDR block `10.0.0.0/16`      |
| IGW              | Internet Gateway for public subnet            |
| NAT Gateway      | For outbound internet from private subnet     |
| Subnets          | Public: `10.0.1.0/24`, Private: `10.0.2.0/24` |
| Route Tables     | Separate for public and private subnets       |
| Bastion Host     | EC2 instance in public subnet                 |
| Backend Instance | EC2 instance in private subnet                |
| Security Groups  | For Bastion and Backend EC2 with SSH rules    |

---

## 🧱 Step-by-Step Instructions

### 1️⃣ Create a VPC
- CIDR block: `10.0.0.0/16`
- Enable **DNS hostnames** for NAT Gateway resolution.

### 2️⃣ Create Subnets
- **Public Subnet:** `10.0.1.0/24` (e.g., `us-east-1a`)
- **Private Subnet:** `10.0.2.0/24` (e.g., `us-east-1a`)

### 3️⃣ Create and Attach Internet Gateway (IGW)
- Create an Internet Gateway.
- Attach it to the VPC.

### 4️⃣ Create NAT Gateway
- Allocate an **Elastic IP**.
- Create the NAT Gateway in the **Public Subnet**.
- Assign the Elastic IP and wait for the NAT Gateway to become available.

### 5️⃣ Configure Route Tables

#### 🔹 Public Route Table:
- Associate with **Public Subnet**
- Add route:  
  `0.0.0.0/0 → IGW`

#### 🔹 Private Route Table:
- Associate with **Private Subnet**
- Add route:  
  `0.0.0.0/0 → NAT Gateway`

### 6️⃣ Configure Security Groups

#### 🔐 Bastion Host SG:
- **Inbound:**
  - SSH (Port 22) from your IP `x.x.x.x/32`
- **Outbound:** Allow all traffic

#### 🔐 Backend EC2 SG:
- **Inbound:**
  - SSH (Port 22) from Bastion Host's private IP range `10.0.1.0/24`
- **Outbound:** Allow all traffic

### 7️⃣ Launch EC2 Instances

#### 🚀 Bastion Host:
- Subnet: Public Subnet
- SG: Bastion SG
- Key Pair: Use your `.pem` file

#### 🚀 Backend Instance:
- Subnet: Private Subnet
- SG: Backend SG
- Key Pair: Use the same `.pem` file

---

## ✅ Validation Steps

### 1. Validate Bastion Access
From your **local machine**:
```bash
ssh -i my-key.pem ec2-user@<bastion-public-ip>

