# Project: 3-Tier Application on AWS

## Overview

This project sets up a 3-tier architecture on AWS, consisting of web, application, and database layers.

---

## Phase 1: Create a VPC

- Create a VPC with the following subnets:
  - 2 Public Subnets
  - 4 Private Subnets

---

## Phase 2: Create an S3 Bucket

- Create an S3 bucket to store and upload your application code.

---

## Phase 3: Create IAM Role

- Create an IAM Role named `EC2ssm`:
  - Purpose: Allow EC2 instances in private subnets to be accessible via AWS Systems Manager (SSM).
  - This replaces the need for SSH access, since these instances do not have public IPs.

---

## Phase 4: Create the Database

1. **DB Subnet Group**

   - Assign 2 private subnets to this DB subnet group.

2. **Database Security Group**

   - Inbound Rules:
     - Port: 3306 (MySQL)
     - Source: VPC CIDR block

3. **Launch MySQL DB (RDS)**

   - DB Type: MySQL
   - Username: 
   - Password: 
   - Endpoint: 

---

## Phase 5: Create MySQL Table (App-Tier)

1. SSH or connect via SSM to your private EC2 instance.
2. Install MySQL client:
   ```bash
   sudo apt install mysql-client-core-8.0
   ```
3. Troubleshooting steps if errors appear:
   - Ensure outbound SG on the EC2 allows port 3306 to the VPC CIDR.
   - Run:
     ```bash
     nslookup project-3tier-db.c4byg4s6wmxz.us-east-1.rds.amazonaws.com
     nc -zv [resolved IP] 3306
     mysql -h project-3tier-db.c4byg4s6wmxz.us-east-1.rds.amazonaws.com -u admin -p
     ```

---

## Phase 6: Configure Application Tier

1. Install Node.js and PM2:
   ```bash
   sudo apt install nodejs npm
   sudo npm install pm2 -g
   ```
2. Download and start your application:
   ```bash
   aws s3 cp s3://project-3tier-bucket/app-tier/ app-tier --recursive
   cd app-tier
   pm2 start index.js
   curl http://localhost:4000/health
   ```
3. Troubleshooting:
   ```bash
   sudo lsof -i :4000
   sudo lsof -i -P -n | grep LISTEN
   ```

---

## Phase 7: Configure Web Tier

1. Install Node.js and Nginx:
   ```bash
   sudo apt install nodejs npm nginx
   ```
2. Download web-tier code and Nginx configuration:
   ```bash
   aws s3 cp s3://project-3tier-bucket/web-tier/ web-tier --recursive
   aws s3 cp s3://project-3tier-bucket/nginx-Without-SSL.conf .
   ```
3. Update the Nginx configuration (e.g., change user to `ubuntu`) and enable the service:
   ```bash
   sudo systemctl enable nginx
   sudo systemctl start nginx
   sudo ss -tulnp | grep nginx
   ```

---

## Phase 8: Create Application Load Balancers

1. Create an **Internal ALB** to route traffic to app-tier on port 4000.
2. Create an **External ALB** to route public traffic to web-tier on port 80.

---

## Phase 9: Configure Auto Scaling Groups (ASGs)

1. Create launch templates from both web and app EC2 instances.
2. Create separate ASGs for web and app tiers.
3. Attach each ASG to the appropriate ALB.

---

## Done!

Your 3-tier application is now fully deployed on AWS with a scalable, secure architecture.

