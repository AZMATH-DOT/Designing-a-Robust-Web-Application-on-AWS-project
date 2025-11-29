# Designing-a-Robust-Web-Application-on-AWS-project
This project demonstrates a complete AWS architecture setup for a scalable, secure, and highly available web application. It includes VPC design, EC2 deployments, load balancing, private networking, NAT gateways, PHP &amp; Apache setup, phpMyAdmin deployment, and RDS integration.
📌 Project Overview
<img width="1536" height="1024" alt="ChatGPT Image Nov 29, 2025, 07_28_07 PM" src="https://github.com/user-attachments/assets/f94d8ada-989e-4e1e-8bc0-d42f16be8a00" />

This challenge focuses on building a 3-tier architecture on AWS:

Networking Layer → VPC, subnets, route tables, NAT & IGW

Compute Layer → EC2 bastion host + private application servers

Application Layer → Apache, PHP 8.2, phpMyAdmin

Database Layer → Amazon RDS (MySQL)

Load Balancing Layer → Application Load Balancer with target groups

The architecture ensures high availability, security, and scalability, while following AWS best practices.

🏗️ Architecture Summary
✔️ VPC Setup

VPC CIDR: 20.0.0.0/20

Public Subnets:

web-pub-sub1 → 20.0.1.0/24 (us-east-1a)

web-pub-sub2 → 20.0.2.0/24 (us-east-1b)

Private App Subnets:

20.0.3.0/24

20.0.4.0/24

Private DB Subnets:

20.0.5.0/24

20.0.6.0/24

✔️ Networking Components

Internet Gateway (IGW)

NAT Gateway for private subnets

Three Route Tables (web, app, db)

Proper routing for public and private traffic

🖥️ EC2 Instances
1. Jump Server / Bastion Host (Public Subnet)

Used for securely accessing private servers

SSH enabled

Public IP assigned

Security Group: jump-sg
![IMG-20251129-WA0005](https://github.com/user-attachments/assets/0aaf3786-e68a-4fd1-a1c6-01a8e90d525d)

2. Application Servers (Private Subnets)

Two EC2 instances hosting:

Apache Web Server

PHP 8.2

phpMyAdmin

Custom landing pages (PHP Server 1 & 2)

Security Group: app-sg (allows traffic only from ALB)

⚙️ Software Installation on App Servers
Install Apache, PHP 8.2 & Dependencies
sudo yum update -y
sudo dnf install php8.2
sudo yum install php8.2-mysqlnd httpd php-mbstring php-xml php-fpm -y

Configure Web Directory
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www

Setup phpMyAdmin
cd /var/www/html
wget https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.tar.gz
mkdir phpMyAdmin
tar -xvzf phpMyAdmin-latest-all-languages.tar.gz -C phpMyAdmin --strip-components 1

⚖️ Load Balancing Layer
✔️ Target Group
![IMG-20251129-WA0006](https://github.com/user-attachments/assets/7e645477-c9c8-42a7-ad74-e3e8665a2bd8)

Created with HTTP protocol

Registered both private EC2 application servers

Health check path: /

✔️ Application Load Balancer

Internet-facing

Security group alb-sg

Listener: HTTP :80

Target group attached

Sticky sessions enabled

When accessed via ALB DNS:

Alternates between “PHP Server 1” and “PHP Server 2”
![IMG-20251129-WA0007](https://github.com/user-attachments/assets/71735634-7f90-4804-b7aa-728f5a5b679e)
yer (RDS MySQL)
✔️ RDS Configuration

Engine: MySQL

Free-tier instance (db.t3.micro)

Private subnets using DB subnet group

Public access: Disabled

Security Group: db-sg (allows only from app-sg)

✔️ Connect phpMyAdmin to RDS

Update phpMyAdmin config file:

nano /var/www/html/phpMyAdmin/config.inc.php

![IMG-20251129-WA0008](https://github.com/user-attachments/assets/3febda71-4e04-4b92-a608-bc253d0259f8)

Replace host:

$cfg['Servers'][$i]['host'] = '<RDS endpoint>';

🔐 Security Best Practices Applied

Private application & DB subnets (no public IPs)

Bastion host for secure SSH access

Restricted Security Groups:

app-sg only accepts traffic from ALB

db-sg only accepts MySQL from app servers

NAT Gateway prevents exposure of private servers

ALB handles all external traffic

✔️ Final Testing
Test Application:

Access ALB DNS

Should show:

PHP Server 1

PHP Server 2 (on refresh)

Test phpMyAdmin:
ALB-DNS/phpMyAdmin


Login using RDS credentials.

📊 Outcome

This project demonstrates the ability to design and deploy a secure, multi-tier web application architecture using AWS core services, implementing:

High availability

Secure networking

Scalable application hosting

Real-world RDS integration

Load balancing

Infrastructure best practices
![IMG-20251129-WA0009](https://github.com/user-attachments/assets/5efa54db-c2f5-4acd-999f-396889ce813d)
![IMG-20251129-WA0004](https://github.com/user-attachments/assets/8261aa96-a941-4094-8e13-589484ce2604)
![IMG-20251129-WA0003](https://github.com/user-attachments/assets/16a7150f-8ff4-4a6b-b070-25565d649a2a)
![WhatsApp Image 2025-11-29 at 17 34 33_4147dc84](https://github.com/user-attachments/assets/029497d9-d9e5-455b-a6e7-cbf6fc7499b6)
