# AWS Project 1 : Deploying a 2-Tier Application Architecture in Private Subnets on AWS

One of the most important concepts in cloud architecture is learning how to securely separate application components using public and private subnets.

In this project, I built and deployed a simple 2-tier architecture on AWS where:

1. The frontend/web server is deployed in a public subnet and accessible from the internet.
2. The backend database server is deployed securely inside a private subnet with no direct internet access.

The goal of this project was to understand:
- AWS VPC networking
- Public vs Private subnets
- Route tables
- Internet Gateway
- Security Groups
- Secure communication between application layers

This project helped me understand how real-world production architectures are designed to improve both security and scalability.

---

# Project Architecture

```text
User
  ↓
Internet Gateway
  ↓
Public Subnet
  ↓
EC2 Web Server
  ↓
Private Subnet
  ↓
MySQL Database Server
```

---

# Prerequisites

To follow along with this project, you only need:

- An AWS account
- Basic understanding of EC2 and VPC
- SSH key pair
- Ubuntu EC2 AMI
- MySQL installed on the database server

---

# AWS Services Used

For this project, I made use of the following AWS services:

- Amazon VPC
- EC2
- Internet Gateway
- Route Tables
- Public & Private Subnets
- Security Groups
- MySQL Database
- NAT Gateway *(Optional)*

---

# Step 1: Create a Custom VPC

I started by creating a custom VPC from the AWS console.

For this setup, I used:

```bash
10.0.0.0/16
```

This CIDR block provides enough private IP addresses for the resources inside the VPC.

From the AWS console:

- Navigate to **VPC Dashboard**
- Select **Create VPC**
- Add VPC name and CIDR block

Once completed, the VPC becomes the isolated network environment for the project.

---

# Step 2: Create Public and Private Subnets

Next, I created two subnets inside the VPC.

## Public Subnet

The public subnet hosts the web/application server.

Example CIDR:

```bash
10.0.1.0/24
```

This subnet allows internet access through an Internet Gateway.

---

## Private Subnet

The private subnet hosts the database server.

Example CIDR:

```bash
10.0.2.0/24
```

Resources in this subnet are not directly accessible from the internet.

This improves security by isolating sensitive backend services.

---

# Step 3: Setup Internet Gateway

To allow internet access for the public subnet, I created and attached an Internet Gateway to the VPC.

From the VPC dashboard:

- Create Internet Gateway
- Attach it to the custom VPC

This enables incoming and outgoing internet traffic for public resources.

---

# Step 4: Configure Route Tables

Next, I configured routing inside the VPC.

## Public Route Table

I added the following route:

```text
0.0.0.0/0 → Internet Gateway
```

and associated it with the public subnet.

This allows the web server to communicate with the internet.

---

## Private Route Table

The private subnet route table only allows internal VPC communication.

This prevents direct public internet access to the database server.

---

# Step 5: Launch the Web Server (Public Subnet)

Next, I launched an Ubuntu EC2 instance inside the public subnet.

For the setup, I used:

- AMI: Ubuntu
- Instance type: t2.micro

I also configured a security group allowing:

- SSH (22)
- HTTP (80)

After connecting to the server via SSH, I installed Nginx:

```bash
sudo apt update
sudo apt install nginx -y
```

Then I started the service:

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

To test the setup, I visited the public IPv4 address of the EC2 instance and confirmed the Nginx default page was accessible.

---

# Step 6: Launch the Database Server (Private Subnet)

Next, I launched another EC2 instance inside the private subnet.

This instance acts as the backend database server.

For security purposes:
- No public IP address was assigned
- SSH access was restricted
- Only the web server could communicate with it

I installed MySQL on the private instance:

```bash
sudo apt update
sudo apt install mysql-server -y
```

---

# Step 7: Configure Security Groups

One of the most important parts of this project was properly configuring security groups.

## Web Server Security Group

Allowed:
- HTTP (80)
- SSH (22)

From:
- Anywhere (`0.0.0.0/0`)

---

## Database Security Group

Allowed:
- MySQL (3306)

From:
- Web Server Security Group only

This ensures the database server only accepts traffic from the application server and not directly from the internet.

---

# Step 8: Test Application Connectivity

After configuring both servers, I tested communication between them.

The application server successfully connected to the MySQL database running inside the private subnet.

This confirmed:
- Route tables were correctly configured
- Security groups were working properly
- Private subnet isolation was successful

---

# Security Benefits of This Architecture

This setup follows a common real-world cloud security practice.

## Database Isolation

The database is hidden from the public internet.

---

## Controlled Communication

Only the application server can communicate with the database server.

---

## Reduced Attack Surface

Sensitive backend resources remain protected inside private subnets.

---

# Optional Improvements

While this project focuses on the core 2-tier architecture, additional services can improve scalability and availability.

## Application Load Balancer (ALB)

Distribute traffic across multiple web servers.

---

## Auto Scaling Group (ASG)

Automatically scale EC2 instances during high traffic.

---

## NAT Gateway

Allow private subnet resources to access the internet securely for updates.

---

## RDS Instead of Self-Managed MySQL

Use Amazon RDS for managed database operations.

---

# Challenges I Faced

Some of the issues I encountered during this project included:

- Incorrect route table associations
- Security group misconfigurations
- Database connectivity troubleshooting
- SSH access issues

Troubleshooting these problems helped me better understand AWS networking and cloud security concepts.

---

# Key Learnings

Through this project, I learned:

- How VPC networking works
- Difference between public and private subnets
- Importance of route tables and gateways
- Security group configuration
- Secure cloud architecture design
- How real-world applications isolate backend services

---

# Conclusion

This project helped me gain practical experience designing and deploying secure cloud infrastructure on AWS.

By implementing a 2-tier architecture with private subnets, I learned how production environments separate frontend and backend resources to improve security, scalability, and reliability.

This project strengthened my understanding of:
- AWS Networking
- VPC Architecture
- Cloud Security
- Infrastructure Design

and gave me hands-on exposure to building secure cloud environments similar to real-world enterprise deployments.
