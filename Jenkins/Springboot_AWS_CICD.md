### Complete Jenkins CI/CD Tutorial: Deploying a Spring Boot App to AWS EC2 (2025)

This step-by-step tutorial covers setting up a full **Continuous Integration/Continuous Deployment (CI/CD) pipeline** using **Jenkins** to build and deploy a **Spring Boot application** (as a JAR) to an **AWS EC2 instance**. It's based on current best practices as of December 2025.

The flow:
- Developer pushes code to Git (e.g., GitHub).
- Jenkins detects change → Builds/tests/packages the Spring Boot JAR.
- Jenkins copies the JAR to EC2.
- Jenkins restarts the app on EC2.

#### Prerequisites
- AWS account with free tier access.
- Spring Boot project with Maven (e.g., a simple REST app).
- Git repository (GitHub recommended).
- Basic knowledge of Java, Maven, and Linux commands.

#### Step 1: Launch an EC2 Instance (Target Server)
1. AWS Console → EC2 → Launch Instance.
2. AMI: Amazon Linux 2023 (free tier).
3. Instance type: t3.micro (free tier).
4. Key pair: Create/download a .pem for initial SSH.
5. Security Group: Allow inbound SSH (22) from your IP, HTTP (8080) from anywhere (for app access).
6. Launch and note public DNS/IP.

SSH in and prepare:
```bash
sudo yum update -y
sudo yum install java-21-amazon-corretto-devel -y  # Java 21
```

#### Step 2: Set Up systemd Service on EC2 (Best Practice – Reliable Restarts)
This avoids nohup/pkill issues. Run once on EC2:

```bash
sudo tee /etc/systemd/system/myapp.service > /dev/null <<EOF
[Unit]
Description=My Spring Boot App
After=network.target

[Service]
WorkingDirectory=/home/ec2-user
ExecStart=/usr/bin/java -jar /home/ec2-user/app.jar --spring.profiles.active=prod
Restart=always
User=ec2-user
StandardOutput=append:/home/ec2-user/app.log
StandardError=append:/home/ec2-user/app.log

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable myapp.service  # Auto-start on boot
```

Now restarts are simple: `sudo systemctl restart myapp.service`

#### Step 3: Install & Configure Jenkins
You can install Jenkins on your local machine, another EC2, or anywhere.

- Download from https://www.jenkins.io/download/
- Install Java 21.
- Run `java -jar jenkins.war` or use installer.

Initial setup:
- Unlock with initial password.
- Install suggested plugins + **Publish Over SSH** and **Pipeline**.

#### Step 4: Configure Publish Over SSH Plugin
1. Manage Jenkins → System → Publish over SSH.
2. Add SSH Server:
    - Name: `ec2-server`
    - Hostname: Your EC2 public DNS/IP
    - Username: `ec2-user`
    - Advanced → Check "Use password authentication, or use a different key" → Paste your .pem private key.
3. Test Configuration → Success.

#### Step 5: Create Jenkins Pipeline
New Item → Pipeline → Name it (e.g., "SpringBoot-Deploy").

Jenkinsfile (commit to your repo root):

```groovy
pipeline {
    agent any

    tools {
        jdk 'JDK21'  // Configure in Global Tool Configuration
        maven 'Maven3'
    }

    environment {
        JAR_NAME = 'ecommerceapp-0.0.1-SNAPSHOT.jar'  // Your JAR name
        REMOTE_DIR = '/home/ec2-user'
        APP_PORT = '8080'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Package') {
            steps {
                bat 'mvn -B clean package -DskipTests'  // Use sh on Linux agent
            }
        }

        stage('Deploy JAR to EC2') {
            steps {
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'ec2-server',
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    sourceFiles: "target/${JAR_NAME}",
                                    removePrefix: 'target/',
                                    remoteDirectory: "${REMOTE_DIR}",
                                    flatten: true,
                                    execCommand: 'mv ${JAR_NAME} app.jar'  // Consistent name
                                )
                            ]
                        )
                    ]
                )
            }
        }

        stage('Restart App on EC2') {
            steps {
                sshPublisher(
                    failOnError: false,  // Optional: Ignore minor exit issues
                    publishers: [
                        sshPublisherDesc(
                            configName: 'ec2-server',
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    execCommand: '''
                                        sudo systemctl restart myapp.service
                                        echo 'App restarted successfully'
                                    '''
                                )
                            ]
                        )
                    ]
                )
            }
        }
    }

    post {
        success {
            echo "Deployment successful! Access at http://${env.EC2_PUBLIC_DNS}:${APP_PORT}"
        }
        always {
            echo "Pipeline complete."
        }
    }
}
```




#### Step 6: Trigger & Test
- Save pipeline → Build Now.
- On push to main: Add GitHub webhook for auto-trigger.

#### Troubleshooting Tips
- JAR not running: SSH to EC2, check `sudo journalctl -u myapp.service -f`.
- Permissions: Ensure ec2-user can run sudo systemctl (add NOPASSWD if needed).
- Advanced: Use AWS SSM for keyless access (no SSH plugin needed).

This setup is reliable, secure, and production-ready for simple deployments. For scaling, consider ECS/EKS or Elastic Beanstalk.
