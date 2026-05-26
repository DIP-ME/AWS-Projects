# AWS Project: Understanding Security Groups vs Network ACLs (NACLs) in AWS

When building secure cloud infrastructure on AWS, one of the most important concepts to understand is how traffic is controlled within a VPC.

In this project, I explored and implemented two important AWS security layers:

1. Security Groups (Instance-level firewall)
2. Network Access Control Lists - NACLs (Subnet-level firewall)

The goal of this project was to understand:
- How AWS controls inbound and outbound traffic
- The difference between stateless and stateful firewalls
- How to secure EC2 instances and subnets
- Real-world network security practices in AWS

By the end of this project, I gained practical experience configuring secure network rules and controlling traffic flow inside a VPC environment.

---

# Project Architecture

```text
Internet
   ↓
Internet Gateway
   ↓
Public Subnet
   ↓
NACL Rules
   ↓
EC2 Instance
   ↓
Security Group Rules
```

---

# Prerequisites

To follow along with this project, you only need:

- An AWS account
- Basic knowledge of VPC and EC2
- A running EC2 instance
- Public and private subnet setup

---

# AWS Services Used

For this project, I made use of:

- Amazon VPC
- EC2
- Security Groups
- Network ACLs (NACLs)
- Internet Gateway
- Route Tables

---

# Understanding Security Groups

A Security Group acts as a virtual firewall attached directly to an EC2 instance.

It controls:
- Inbound traffic
- Outbound traffic

Security Groups are **stateful**, meaning:
- If inbound traffic is allowed, the response traffic is automatically allowed back.

---

# Step 1: Create an EC2 Instance

I started by launching an Ubuntu EC2 instance inside a public subnet.

For this setup, I used:

- AMI: Ubuntu
- Instance type: t2.micro

---

# Step 2: Configure Security Group

While creating the instance, I configured a custom Security Group.

## Inbound Rules

Allowed:
- SSH (22)
- HTTP (80)

From:
```text
0.0.0.0/0
```

---

## Outbound Rules

Allowed:
- All traffic

This allowed the instance to:
- Receive SSH and HTTP traffic
- Send responses back to users

---

# Step 3: Test Security Group Rules

After launching the instance:

- I connected via SSH
- Installed Nginx
- Tested HTTP access from the browser

Commands used:

```bash
sudo apt update
sudo apt install nginx -y
```

Then visited:

```text
http://<public-ip>
```

The web server loaded successfully, confirming the Security Group rules were working properly.

---

# Understanding Network ACLs (NACLs)

A Network ACL acts as a firewall at the subnet level.

Unlike Security Groups, NACLs are **stateless**, meaning:
- Both inbound and outbound rules must be explicitly allowed.

NACLs control traffic entering and leaving the subnet itself.

---

# Step 4: Create Custom NACL

From the VPC dashboard:

- Navigate to **Network ACLs**
- Create a custom NACL
- Associate it with the subnet

---

# Step 5: Configure Inbound NACL Rules

I configured the following inbound rules:

| Rule # | Type | Protocol | Port Range | Source | Allow/Deny |
|---|---|---|---|---|---|
| 100 | HTTP | TCP | 80 | 0.0.0.0/0 | ALLOW |
| 110 | SSH | TCP | 22 | My IP | ALLOW |

This allowed:
- Web traffic from anywhere
- SSH access only from my local IP

---

# Step 6: Configure Outbound NACL Rules

Next, I configured outbound rules:

| Rule # | Type | Protocol | Port Range | Destination | Allow/Deny |
|---|---|---|---|---|---|
| 100 | HTTP | TCP | 80 | 0.0.0.0/0 | ALLOW |
| 110 | HTTPS | TCP | 443 | 0.0.0.0/0 | ALLOW |

Since NACLs are stateless, outbound responses must also be explicitly allowed.

---

# Step 7: Testing NACL Behavior

To better understand how NACLs work, I experimented with different rules.

## Blocking HTTP Traffic

I created a DENY rule for port 80.

Result:
- The website became inaccessible even though the Security Group allowed HTTP traffic.

This demonstrated that:
- NACL rules are evaluated before traffic reaches the EC2 instance.

---

# Security Groups vs NACLs

One of the biggest takeaways from this project was understanding the differences between Security Groups and NACLs.

| Feature | Security Group | NACL |
|---|---|---|
| Level | Instance Level | Subnet Level |
| Stateful/Stateless | Stateful | Stateless |
| Allow/Deny Rules | Allow Only | Allow & Deny |
| Applies To | EC2 Instance | Entire Subnet |
| Rule Evaluation | All Rules Evaluated | Rules Evaluated in Order |
| Default Behavior | Deny Inbound | Allow/Deny Based on Rules |

---

# Real-World Use Cases

## Security Groups

Used for:
- Instance-specific access control
- Web servers
- Databases
- Application security

---

## NACLs

Used for:
- Subnet-level protection
- Blocking malicious IPs
- Additional security layer
- Compliance requirements

---

# Security Best Practices

## Restrict SSH Access

Avoid:
```text
0.0.0.0/0
```

Use:
```text
Your Public IP
```

---

## Follow Least Privilege Principle

Only allow ports and traffic that are required.

---

## Use NACLs as Additional Protection

Security Groups alone are not always enough.

---

## Separate Public and Private Subnets

Keep sensitive resources inside private subnets.

---

# Challenges I Faced

Some issues I encountered during this project included:

- Accidentally blocking all inbound traffic using NACL rules
- Forgetting outbound rules in NACLs
- Confusion between stateful and stateless behavior
- Troubleshooting subnet associations

Working through these issues improved my understanding of AWS networking and security concepts.

---

# Key Learnings

Through this project, I learned:

- Difference between Security Groups and NACLs
- Stateful vs Stateless firewalls
- How AWS filters network traffic
- How subnet-level security works
- Importance of layered cloud security

---

# Conclusion

This project gave me practical hands-on experience implementing AWS network security using Security Groups and Network ACLs.

Understanding how these two services work together is essential for designing secure cloud environments.

By configuring both instance-level and subnet-level protection, I gained a deeper understanding of:
- AWS VPC security
- Traffic filtering
- Network isolation
- Cloud security best practices

This project strengthened my ability to design and troubleshoot secure AWS architectures similar to real-world production environments.
