## Amazon EC2

#### What is Amazon EC2?
Amazon Elastic Compute Cloud (Amazon EC2) is a core AWS service that delivers secure, resizable compute capacity in the cloud, enabling you to launch virtual servers (called **instances**) to run applications, host websites, process data, or perform any computational task. Launched in 2006, EC2 forms the backbone of AWS's Infrastructure as a Service (IaaS) offerings, allowing developers and IT teams to provision computing resources on-demand without investing in physical hardware. As of December 2025, EC2 supports over 750 instance types across more than 30 AWS Regions, powering everything from simple web apps to high-performance computing (HPC) workloads like AI training.

At its heart, EC2 abstracts the complexity of server management: You specify your needs (e.g., CPU cores, memory, storage), and AWS handles the underlying virtualization, networking, and scaling. This elasticity means you can start with a single small instance and scale to thousands in minutes, paying only for active usage. EC2 integrates seamlessly with other AWS services like Amazon S3 for storage, Amazon RDS for databases, and Amazon VPC for networking, making it ideal for building full-stack applications.

**Key Concepts in EC2**:
- **Instances**: The virtual machines you launch. Each runs an operating system (e.g., Amazon Linux, Ubuntu, Windows) and your code. Lifecycle: Launch → Run → Stop/Terminate.
- **Amazon Machine Images (AMIs)**: Pre-configured templates (OS + software) for instances. AWS provides public AMIs (e.g., Amazon Linux 2023); create custom ones from snapshots.
- **Instance Types**: Hardware configurations categorized by use case:
    - **General Purpose** (e.g., t4g, m7g): Balanced CPU/memory for web servers/apps.
    - **Compute Optimized** (e.g., c7g): High CPU for batch processing.
    - **Memory Optimized** (e.g., r7g): For databases/in-memory analytics.
    - **Storage Optimized** (e.g., i4i): For high I/O like NoSQL databases.
    - **Accelerated Computing** (e.g., g5 with NVIDIA GPUs): For ML/graphics.
    - Latest (2025): New **X8aedz instances** powered by 5th Gen AMD EPYC processors, offering up to 75 Gbps network bandwidth and Elastic Fabric Adapter (EFA) support for memory-intensive workloads like large-scale databases.
- **Pricing Models** (Flexible and Cost-Effective):
    - **On-Demand**: Pay per hour/second (~$0.0058/hour for t3.micro Linux).
    - **Savings Plans/Reserved Instances**: Up to 72% off for 1-3 year commitments.
    - **Spot Instances**: Up to 90% discount for interruptible workloads (e.g., batch jobs).
    - **Dedicated Hosts/Instances**: For compliance (e.g., BYOL licenses).
    - Free Tier (Updated 2025): For accounts created before July 15, 2025, 750 hours/month of t3.micro/t2.micro Linux/Windows instances for 12 months. New accounts (post-July 15, 2025) get $100 in free credits for the first year, covering basic EC2 usage.
- **Security**:
    - **Security Groups**: Virtual firewalls controlling inbound/outbound traffic (stateful, like iptables).
    - **Key Pairs**: SSH/RDP for secure access (public key stored on instance).
    - **IAM Roles**: Attach to instances for temporary AWS API access (no hard-coded credentials).
    - **Encryption**: At rest (EBS volumes) and in transit (TLS).
    - **Isolation**: Instances run in isolated hypervisors; Nitro System (2025 standard) adds hardware root-of-trust for confidentiality.
- **Networking**: Launch in a VPC with Elastic IPs (static public IPs), Elastic Network Interfaces (ENIs), and integration with AWS Direct Connect/VPN for hybrid setups.
- **Storage**: Attach EBS volumes (block storage, up to 64 TB) or instance store (ephemeral, fast SSD).
- **Monitoring & Management**: Amazon CloudWatch for metrics/alarms; AWS Systems Manager for patching/automation.

**Recent 2025 Updates (from re:Invent 2025)**:
- **AWS Lambda Managed Instances**: A new hybrid capability allowing Lambda functions to run on EC2-like infrastructure for serverless flexibility with EC2 control.
- Enhanced Nitro Enclaves for confidential computing (e.g., secure AI model training).
- Broader Graviton4 (Arm-based) support across more instance types for 20-40% better price/performance.

EC2's global footprint (105+ Availability Zones) ensures 99.99% SLA uptime when architected for high availability (e.g., across multiple AZs). It's the go-to for deploying apps like your Spring Boot project, offering everything from microservices to monolithic servers.

#### Why Use EC2 for Spring Boot Apps?
EC2 provides a flexible runtime for Java apps: Run JARs on Linux instances, scale with Auto Scaling Groups, and integrate with load balancers. Combined with RDS for databases, it creates a robust, managed stack.

### Detailed Tutorial: Deploying a Spring Boot App to EC2 with RDS

This hands-on tutorial deploys a simple Spring Boot REST app (e.g., with a MySQL dependency) to an EC2 instance, using RDS as the backend database. We'll use the AWS Console for simplicity (CLI options noted). Assumes you have:
- An AWS account (free tier eligible).
- A Spring Boot project with Maven (e.g., from start.spring.io: Add Web, JPA, MySQL dependencies).
- Built JAR (e.g., `mvn clean package` → `target/myapp-0.0.1-SNAPSHOT.jar`).
- Basic SSH knowledge.

**Time Estimate**: 30-45 minutes. **Cost**: Free tier (~$0 if under limits); monitor via Billing Dashboard.

#### Step 1: Launch an EC2 Instance
1. **Navigate to EC2**:
    - AWS Console → EC2 → Launch Instance.

2. **Choose AMI**:
    - Select **Amazon Linux 2023** (free tier, lightweight).

3. **Instance Type**:
    - **t3.micro** (1 vCPU, 1 GB RAM—free tier eligible).

4. **Key Pair**:
    - Create new → Name it (e.g., `my-key-pair`) → Download `.pem` file.
    - Secure it: `chmod 400 my-key-pair.pem` (Linux/macOS).

5. **Network Settings**:
    - VPC: Default.
    - Security Group: Create new → Allow:
        - SSH (22) from Your IP (for access).
        - HTTP (80) or Custom TCP (8080) from Anywhere (0.0.0.0/0) for app traffic.

6. **Storage**: Default 8-30 GB gp3 (free tier: 30 GB/month).

7. **Launch**:
    - Instance created. Note **Public IPv4 DNS** (e.g., `ec2-3-123-45-67.compute-1.amazonaws.com`).

8. **SSH In**:
   ```
   ssh -i my-key-pair.pem ec2-user@your-ec2-public-dns
   ```
    - Update: `sudo yum update -y`.
    - Install Java: `sudo yum install java-21-amazon-corretto-devel -y` (for Spring Boot 3+).

#### Step 2: Create an RDS MySQL Database
1. **Navigate to RDS**:
    - Console → RDS → Create Database → Standard Create.

2. **Engine**:
    - MySQL → Free Tier → Version 8.0.36 (latest 2025).

3. **Template**: Free Tier.

4. **Settings**:
    - Identifier: `myapp-db`.
    - Username: `admin`.
    - Password: Strong (e.g., `MySecurePass123!`—note it).
    - Instance: db.t3.micro.
    - Storage: 20 GB gp3.

5. **Connectivity**:
    - VPC: Same as EC2 (default).
    - Public Access: Yes (for testing; No for prod).
    - Database Name: `myappdb`.

6. **Additional**:
    - Backup: Enable (1 day retention).
    - Monitoring: Basic.

7. **Create**: Wait ~5-10 mins for "Available".

8. **Security Group**:
    - RDS Console → Connectivity → Edit Inbound Rules.
    - Add: MYSQL (3306) from EC2's Security Group ID (select it—secure inter-service access).

9. **Note Endpoint**: `myapp-db.xxxxxxx.us-east-1.rds.amazonaws.com:3306`.

**CLI Alternative**:
```
aws rds create-db-instance --db-instance-identifier myapp-db --db-instance-class db.t3.micro --engine mysql --master-username admin --master-user-password MySecurePass123! --allocated-storage 20 --db-name myappdb --region us-east-1
```

#### Step 3: Configure Spring Boot for RDS
1. **Update application-prod.properties** (in `src/main/resources`):
   ```
   spring.datasource.url=jdbc:mysql://your-rds-endpoint:3306/myappdb?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
   spring.datasource.username=admin
   spring.datasource.password=MySecurePass123!
   spring.jpa.hibernate.ddl-auto=update  # Auto-creates tables
   spring.jpa.show-sql=true
   server.port=8080
   ```

2. **Rebuild JAR**:
   ```
   mvn clean package -Pprod
   ```
   (Add `-Pprod` profile if using Maven profiles.)

3. **Upload JAR to EC2**:
    - From local: `scp -i my-key-pair.pem target/myapp-0.0.1-SNAPSHOT.jar ec2-user@your-ec2-public-dns:~/app.jar`.

#### Step 4: Deploy and Run the App on EC2
1. **SSH into EC2** (if not already).

2. **Run the App**:
   ```
   nohup java -jar app.jar --spring.profiles.active=prod > app.log 2>&1 &
   ```
    - `nohup`: Runs detached from session.
    - Background (`&`): Frees terminal.
    - Logs to `app.log`.

3. **Verify**:
    - Logs: `tail -f app.log` (look for "Started Application" and DB connection success).
    - App Access: `http://your-ec2-public-dns:8080` (e.g., `/health` endpoint).
    - DB Test: From EC2, `sudo yum install mysql -y && mysql -h your-rds-endpoint -u admin -p -e "SHOW DATABASES;"`.

#### Step 5: Make It Production-Ready (Optional Enhancements)
- **Systemd Service** (Auto-Restart):
  ```
  sudo tee /etc/systemd/system/myapp.service > /dev/null <<EOF
  [Unit]
  Description=Spring Boot App
  After=network.target

  [Service]
  WorkingDirectory=/home/ec2-user
  ExecStart=/usr/bin/java -jar /home/ec2-user/app.jar --spring.profiles.active=prod
  Restart=always
  User=ec2-user

  [Install]
  WantedBy=multi-user.target
  EOF
  sudo systemctl daemon-reload
  sudo systemctl enable --now myapp.service
  ```
    - Restart: `sudo systemctl restart myapp.service`.

- **Security**:
    - Use IAM Roles for EC2 (attach RDS access policy).
    - Enable HTTPS: Add SSL cert via AWS Certificate Manager + Elastic Load Balancer.
    - Environment Vars: Set DB creds as EC2 env vars (not in JAR).

- **Scaling**:
    - Auto Scaling Group: Console → EC2 → Auto Scaling → Create (min 2 instances).
    - Load Balancer: Application Load Balancer to distribute traffic.

- **Monitoring**:
    - CloudWatch: Enable agent on EC2 (`sudo yum install amazon-cloudwatch-agent`).
    - RDS: Performance Insights for query tuning.

- **Cleanup**:
    - Stop EC2: Console → Instances → Stop (saves hours).
    - Delete RDS: Actions → Delete (backup first).
    - Delete Key Pair: If unused.

**CLI Deployment Snippet**:
```
# Upload via SCP (from local)
scp -i key.pem app.jar ec2-user@ec2-dns:~/

# SSH and run
ssh -i key.pem ec2-user@ec2-dns 'nohup java -jar app.jar --spring.profiles.active=prod > app.log 2>&1 &'
```

**Common Issues**:
- **DB Connection Failed**: Check security groups (EC2 SG in RDS inbound), endpoint/port, creds.
- **Port Not Open**: Update EC2 security group for 8080.
- **Out of Memory**: t3.micro is limited; monitor with `free -h`.
- **2025 Note**: Use Amazon Q for automated troubleshooting (console integration).

This setup deploys a resilient Spring Boot + RDS stack. For CI/CD (e.g., Jenkins), extend with scripts. Scale to ECS for containers next!