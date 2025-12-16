## Jenkins Basics 
#### 1. Introduction to Jenkins
- Jenkins is an **open-source automation server** written in Java.
- Primary purpose: Automate parts of the software development lifecycle, especially **building**, **testing**, and **deploying** applications.
- Supports **Continuous Integration (CI)**, **Continuous Delivery (CD)**, and **Continuous Deployment**.
- Originally developed as "Hudson" by Kohsuke Kawaguchi in 2004 at Sun Microsystems.
- In 2011, after a dispute with Oracle, the community forked it and renamed it **Jenkins**.
- Governed by the Continuous Delivery Foundation (part of the Linux Foundation).
- Extremely popular due to its **flexibility** and **extensibility** — over 2,000 community-contributed plugins.

#### 2. Core Concepts and Theory
- **Continuous Integration (CI)**:
    - Developers integrate code changes frequently (multiple times a day) into a shared repository.
    - Each integration is verified by an automated build and automated tests.
    - Goal: Detect errors quickly and improve software quality.
- **Continuous Delivery (CD)**:
    - Extension of CI — ensures that code is always in a deployable state.
    - Every change that passes automated tests can be released to production manually.
- **Continuous Deployment**:
    - Every change that passes tests is automatically deployed to production (no manual approval needed).
- **Pipeline as Code**:
    - The most important modern concept in Jenkins.
    - Instead of configuring jobs via the web UI, the entire CI/CD process is defined in code (in a file called **Jenkinsfile**).
    - Jenkinsfile is stored in version control (e.g., Git) alongside the application code.
    - Benefits:
        - Version control for pipelines.
        - Code review for pipeline changes.
        - Reproducibility across environments.
        - Single source of truth.
- **Pipeline Syntax Types**:
    - **Declarative Pipeline**: Structured, easier to read, recommended for most use cases.
    - **Scripted Pipeline**: More flexible, uses Groovy scripting (older style, powerful for complex logic).

#### 3. Jenkins Architecture
- **Controller-Agent Model** (previously called Master-Slave):
    - **Controller (formerly Master)**:
        - Runs the Jenkins web UI.
        - Stores configuration and job history.
        - Schedules builds and dispatches tasks.
        - Monitors agents.
    - **Agents (formerly Slaves/Nodes)**:
        - Execute the actual build/test/deploy steps.
        - Can run on separate machines, VMs, containers (Docker), or Kubernetes pods.
        - Allows distributed builds for better performance, isolation, and scalability.
    - Communication: Controller and agents connect via JNLP (Java Network Launch Protocol) or SSH.
- **Why distributed?**
    - Different jobs may require different environments (e.g., Windows vs Linux, specific tools).
    - Parallel execution speeds up large projects.

#### 4. Key Components and Terminology
- **Job/Project**: A single automation task (e.g., build a Java app).
    - Types:
        - **Freestyle project**: Configured via GUI (legacy, simple tasks).
        - **Pipeline**: Code-based (preferred modern approach).
        - **Multibranch Pipeline**: Automatically creates pipelines for each branch/PR in a repo.
        - **Organization Folder**: Scans GitHub/GitLab orgs and auto-creates multibranch projects.
- **Build**: An execution of a job.
- **Stage**: Logical grouping in a pipeline (e.g., Build, Test, Deploy). Visible in UI.
- **Step**: Individual task within a stage (e.g., run a shell command, compile code).
- **Artifact**: Files produced by a build (e.g., JAR, Docker image) that can be archived.
- **Workspace**: Directory on an agent where source code is checked out and work happens.

#### 5. Key Features
- **Extensive Plugin Ecosystem**: Integrates with almost any tool (Git, SVN, Docker, Kubernetes, Maven, Gradle, SonarQube, Slack, Email, AWS, Azure, etc.).
- **Pipeline Features**:
    - Stages and parallel execution.
    - Input steps (manual approval).
    - Post-actions (always run cleanup, notifications).
    - Timeouts, retries, conditional execution.
- **Scalability**: Supports thousands of jobs and agents.
- **Security**:
    - User authentication (LDAP, OAuth, etc.).
    - Role-Based Access Control (RBAC) via plugins.
    - Secure credential storage (encrypted).
- **Visualization**:
    - Classic **Stage View**: Table showing pipeline stages.
    - **Blue Ocean** (plugin): Modern, user-friendly pipeline visualization (though less actively developed now).
- **Durability**: Pipelines can survive Jenkins restarts.
- **Configuration as Code (CasC)**: Define Jenkins configuration in YAML files for reproducible setups.

#### 6. Basic Declarative Pipeline Example (Jenkinsfile)
```
pipeline {
    agent any                     // Run on any available agent
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/example/repo.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'   // Example for Maven
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying to staging...'
                // Deployment commands here
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
            junit '**/target/surefire-reports/*.xml'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
```
