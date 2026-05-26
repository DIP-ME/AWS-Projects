# AWS Project: Deploying a 2-Tier Application in Private Subnets on AWS

Modern cloud applications are designed with security, scalability, and isolation in mind. One of the most common architecture patterns used in AWS is the **2-tier architecture**, where the frontend application layer is separated from the backend database layer.

In this project, I’ll walk you through deploying a simple 2-tier application on AWS using:

1.  A **Web/Application Server** running inside a public subnet
2.  A **Database Server** running securely inside a private subnet

This setup ensures that:
- Users can access the application publicly
- The database remains protected from direct internet access
- Only the application server can communicate with the database

By the end of this project, you’ll understand:
- Public vs Private Subnets
- VPC networking
- Route tables
- Internet Gateway
- Security Groups
- Secure communication between application layers

Prerequisites
-------------

To follow along, you only need:

*   An AWS account
*   Basic understanding of EC2 and VPC
*   SSH key pair for EC2 access
*   A simple web application (HTML/PHP/Node.js/Python)

For this project, we’ll use:
- Ubuntu EC2 instances
- MySQL database
- Custom VPC networking

Step 1: Create a Custom VPC
---------------------------

We’ll start by creating our own Virtual Private Cloud (VPC).

Navigate to:
**AWS Console → VPC → Create VPC**

For this project, I’ll be using:

*   **VPC Name**: two-tier-vpc
*   **IPv4 CIDR Block**: 10.0.0.0/16

![captionless image](https://miro.medium.com/v2/resize:fit:1400/1*B6nX2mB7R7FvO8T1g1fQWA.png)

Once created, this VPC will act as the isolated network for our application.

Step 2: Create Public and Private Subnets
-----------------------------------------

Inside the VPC, we’ll create two subnets:

1.  Public Subnet → for the Web Server
2.  Private Subnet → for the Database

Navigate to:
**VPC → Subnets → Create subnet**

### Public Subnet

*   **Subnet Name**: public-subnet
*   **Availability Zone**: us-east-1a
*   **CIDR Block**: 10.0.1.0/24

Enable:
- Auto-assign public IPv4 address

![captionless image](https://miro.medium.com/v2/resize:fit:1400/1*9Wb5M4M8vJjY5k7a2yX5ZA.png)

### Private Subnet

*   **Subnet Name**: private-subnet
*   **Availability Zone**: us-east-1a
*   **CIDR Block**: 10.0.2.0/24

Do NOT enable public IP assignment.

![captionless image](https://miro.medium.com/v2/resize:fit:1400/1*8R6vJ0vB8g8r2mY9g2kV5w.png)

Step 3: Create and Attach Internet Gateway
------------------------------------------

To allow internet access for resources in the public subnet, we need an Internet Gateway.

Navigate to:
**VPC → Internet Gateways → Create internet gateway**

*   **Name**: two-tier-igw

After creation:
- Attach it to the VPC

![captionless image](https://miro.medium.com/v2/resize:fit:1400/1*P3mY8nK8q5mR4oW7f6vV8A.png)

Step 4: Configure Route Tables
------------------------------

Route tables determine how traffic flows within the VPC.

### Public Route Table

Navigate to:
**VPC → Route Tables → Create route table**

*   **Name**: public-route-table

Add route:

```text
0.0.0.0/0 → Internet Gateway
```

Associate:
- Public subnet

![captionless image](https://miro.medium.com/v2/resize:fit:1400/1*L6xQ8wB9sR5vA2nJ7gW3LA.png)

### Private Route Table

Create another route table:

*   **Name**: private-route-table

Associate:
- Private subnet

No internet route is required.

Step 5: Create Security Groups
------------------------------

We’ll create separate security groups for:
- Web Server
- Database Server

### Web Server Security Group

Allow inbound:
- SSH (22)
- HTTP (80)

Source:
```text
0.0.0.0/0
```

![captionless image](https://miro.medium.com/v2/resize:fit:1400/1*4kL9Y3M7rQ5bP2xN7sF5WA.png)

### Database Security Group

Allow inbound:
- MySQL/Aurora (3306)

Source:
- Web Server Security Group only

This ensures only the application server can access the database.

![captionless image](https://miro.medium.com/v2/resize:fit:1400/1*2fQ7L5wB6sM8nJ4xV3zP7A.png)

Step 6: Launch the Web Server EC2 Instance
------------------------------------------

Navigate to:
**EC2 → Launch Instance**

For this project, I’ll be using:

*   **AMI**: Ubuntu 24.04
*   **Instance type**: t2.micro
*   **Subnet**: public-subnet
*   **Security Group**: web-server-sg

Enable:
- Auto-assign public IP

![captionless image](https://miro.medium.com/v2/resize:fit:1400/1*7Wg4N8mJ6xQ2P5vK9yF2ZA.png)

Once launched, connect via SSH:

```bash
ssh -i key.pem ubuntu@public-ip
```

Install Nginx:

```bash
sudo apt update
sudo apt install nginx -y
```

Verify installation:

```bash
http://<public-ip>
```

You should see the default Nginx page.

Step 7: Launch the Database Server
----------------------------------

For the database layer, we’ll launch an EC2 instance inside the private subnet.

Navigate to:
**EC2 → Launch Instance**

Use:
*   **AMI**: Ubuntu 24.04
*   **Subnet**: private-subnet
*   **Security Group**: database-sg

Do NOT assign a public IP.

![captionless image](https://miro.medium.com/v2/resize:fit:1400/1*8gQ6M4xB7sW5N2vJ3kL8PA.png)

Install MySQL:

```bash
sudo apt update
sudo apt install mysql-server -y
```

Allow remote MySQL connections from the web server.

Step 8: Connect Web Server to Database
--------------------------------------

From the web server, test connectivity to the database server:

```bash
mysql -h <private-ip> -u root -p
```

If configured correctly:
- The web server should successfully connect
- External users should NOT access the database directly

This demonstrates proper private subnet isolation.

Step 9: Test the Application
----------------------------

Deploy a simple application on the web server that interacts with the database.

Example:
- PHP app
- Node.js app
- Python Flask app

Workflow:

```text
User
 ↓
Web Server (Public Subnet)
 ↓
Database Server (Private Subnet)
```

The application should:
- Receive user requests
- Query the database
- Return responses securely

Conclusion
----------

This hands-on project demonstrated how to build a secure 2-tier application architecture on AWS using public and private subnets.

With this setup:

*   The web server is publicly accessible
*   The database remains isolated and secure
*   Communication between layers is tightly controlled using security groups and subnet routing

This architecture pattern is commonly used in:
- Web applications
- Internal business systems
- Production cloud environments

By completing this project, you gained practical experience with:
- AWS VPC networking
- Public and private subnets
- Internet Gateway
- Route tables
- Security Groups
- Secure application deployment

Understanding this architecture is an important step toward becoming a Cloud or DevOps Engineer.
