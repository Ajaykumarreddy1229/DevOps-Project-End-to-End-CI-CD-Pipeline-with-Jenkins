# Jenkins Controller-Agent Architecture & SonarQube Integration

> **Topic:** Jenkins Controller/Agent Architecture + SonarQube Integration
> **Practice:** Hands-on DevOps Learning
> **Environment:** AWS EC2, Amazon Linux, Java, Maven, Git

---

# 1. Project Overview

This project documents my hands-on practice with:

```text
Jenkins Controller/Agent Architecture
Jenkins Pipeline Agents
AWS EC2
Java
Maven
Git
SSH-Based Jenkins Agents
SonarQube
Jenkins + SonarQube Integration
```

The main objectives were:

```text
1. Understand Jenkins Controller/Agent architecture
2. Distribute build workloads across multiple machines
3. Configure EC2 instances as Jenkins agents
4. Connect Jenkins agents using SSH
5. Understand SonarQube code analysis
6. Integrate SonarQube with a Jenkins CI pipeline
```

---

# 2. Jenkins Controller & Agent Architecture

## What is Jenkins Controller?

The **Jenkins Controller** is the central Jenkins server that manages the Jenkins environment.

It is responsible for tasks such as:

* Managing jobs
* Scheduling builds
* Managing agents
* Managing credentials
* Managing Jenkins configuration
* Coordinating pipeline execution

---

# 3. What is a Jenkins Agent?

A **Jenkins Agent** is a machine that executes build and pipeline workloads assigned by the Jenkins Controller.

Agents can be separate EC2 instances or other machines connected to Jenkins.

---

# 4. Why Use Jenkins Agents?

When Jenkins handles many builds, running every workload directly on the controller can increase its workload.

Agents allow Jenkins to distribute workloads across multiple machines.

### Without Agents

```text
                 Jenkins Controller
                       |
             ┌─────────┼─────────┐
             ↓         ↓         ↓
           Build 1   Build 2   Build 3
             |
        High Workload
```

### With Agents

```text
                  Jenkins Controller
                         |
              ┌──────────┴──────────┐
              ↓                     ↓
           Agent 1                Agent 2
              |                     |
           Build 1               Build 2
              |                     |
           Build 3               Build 4
```

This allows workloads to be distributed across multiple machines.

---

# 5. Jenkins Controller-Agent Architecture

```text
                  +------------------------+
                  |   Jenkins Controller   |
                  |                        |
                  |  Manage & Schedule     |
                  |       Builds           |
                  +-----------+------------+
                              |
                    Jenkins Communication
                              |
              +---------------+---------------+
              |                               |
              ↓                               ↓
      +---------------+               +---------------+
      |    Agent 1    |               |    Agent 2    |
      |    slave1     |               |    slave2     |
      |               |               |               |
      | Java          |               | Java          |
      | Maven         |               | Maven         |
      | Git           |               | Git           |
      +---------------+               +---------------+
```

> Jenkins documentation increasingly uses **Controller** and **Agent** terminology instead of **Master** and **Slave**.

---

# 6. Jenkins Agent Requirements

An EC2 instance used as a Jenkins agent needs the required software and connectivity.

Typical requirements:

```text
Amazon Linux
Java
Maven
Git
SSH Connectivity
Jenkins Agent Configuration
```

The exact Java version should match the Jenkins and application requirements of the environment.

---

# 7. Agent EC2 Setup

For this practice, I created EC2 instances and configured them as Jenkins agents.

Example:

```text
Jenkins Controller
       |
       ├── Agent 1
       |
       └── Agent 2
```

Each agent can have the tools required to execute the assigned builds.

---

# 8. Install Java, Maven and Git

Example command:

```bash
sudo yum install java-21-amazon-corretto-devel maven git -y
```

Check Java:

```bash
java -version
```

Check Maven:

```bash
mvn --version
```

Check Git:

```bash
git --version
```

---

# 9. Configure Jenkins Agent

From the Jenkins Controller:

```text
Manage Jenkins
      ↓
Nodes
      ↓
New Node
      ↓
Permanent Agent
```

---

# 10. Agent Configuration

Example configuration:

```text
Node Name        : slave1

Executors        : 3

Remote Directory : /tmp

Labels           : slave1
```

---

# 11. What are Executors?

An **executor** represents a slot on an agent where Jenkins can run a build.

Example:

```text
Executors = 3
```

Conceptually:

```text
Agent 1
  |
  ├── Executor 1 → Build A
  ├── Executor 2 → Build B
  └── Executor 3 → Build C
```

The number of executors should be selected according to the resources and workload of the agent.

---

# 12. What are Labels?

Labels are used to identify specific Jenkins agents.

Example:

```text
Label:
slave1
```

A pipeline can use the label to request a specific agent.

Example:

```groovy
agent {
    label 'slave1'
}
```

This tells Jenkins to run the pipeline on an agent matching that label.

---

# 13. SSH Agent Configuration

The Jenkins Controller can connect to an EC2 agent using SSH.

Typical configuration:

```text
Launch Method
      ↓
Launch agents via SSH
```

### Host

```text
Private IP of Agent
```

### Credentials

```text
SSH Username with private key
```

### Username

```text
ec2-user
```

---

# 14. SSH Communication

The basic communication flow is:

```text
Jenkins Controller
        |
        | SSH
        ↓
Jenkins Agent
        |
        ↓
Execute Build
```

The Jenkins Controller manages the agent, while the agent performs the assigned build work.

---

# 15. Jenkins Credentials

SSH private keys should be stored securely in Jenkins Credentials.

They should **not** be written directly into the Jenkinsfile.

They should also never be committed to GitHub.

Conceptually:

```text
Jenkins Controller
       |
       ↓
Jenkins Credentials
       |
       ↓
SSH Private Key
       |
       ↓
Agent Connection
```

---

# 16. Monitoring Jenkins Agents

Jenkins provides information about configured nodes and agents.

Useful information includes:

```text
Load Statistics
System Information
Build History
Executor Status
Agent Availability
```

This helps understand how workloads are being distributed.

---

# 17. SonarQube

## What is SonarQube?

**SonarQube** is a code-quality and code-security analysis platform.

It can help identify issues such as:

```text
Bugs
Code Smells
Duplicate Code
Security Vulnerabilities
Other Code Quality Issues
```

SonarQube can be integrated into a CI pipeline so that source code is analyzed during the build process.

---

# 18. Why Use SonarQube?

A basic CI pipeline may look like:

```text
Code
 ↓
Compile
 ↓
Test
 ↓
Package
```

With SonarQube:

```text
Code
 ↓
Compile
 ↓
Test
 ↓
SonarQube Analysis
 ↓
Package
```

This adds automated code-quality analysis to the CI process.

---

# 19. SonarQube Setup

For this practice, I configured SonarQube on a separate EC2 instance.

Example environment:

```text
AWS EC2
   ↓
Amazon Linux
   ↓
Java
   ↓
SonarQube
   ↓
Port 9000
```

SonarQube can be accessed through:

```text
http://<SONARQUBE-IP>:9000
```

> Do not publish real server IP addresses, passwords, tokens, or other sensitive credentials in a public GitHub repository.

---

# 20. Jenkins + SonarQube Integration

Jenkins can be configured to communicate with the SonarQube server.

Typical components include:

```text
SonarQube Scanner
Maven Integration
SonarQube Quality Gates
```

---

# 21. Configure SonarQube in Jenkins

The SonarQube server can be configured from:

```text
Manage Jenkins
      ↓
System
      ↓
SonarQube Servers
```

The configuration connects Jenkins with the SonarQube server.

---

# 22. SonarQube Authentication

Authentication tokens should be stored securely using Jenkins Credentials.

Conceptually:

```text
Jenkins
   |
   ↓
Jenkins Credentials
   |
   ↓
SonarQube Token
   |
   ↓
SonarQube Server
```

Avoid putting authentication tokens directly inside the Jenkinsfile.

---

# 23. SonarQube Analysis Flow

```text
Developer
    |
    ↓
Git Repository
    |
    ↓
Jenkins
    |
    ↓
Build Application
    |
    ↓
Run Tests
    |
    ↓
SonarQube Scanner
    |
    ↓
SonarQube Server
    |
    ↓
Code Analysis
```

---

# 24. Jenkins Pipeline Flow

The complete CI flow can be represented as:

```text
Developer
    |
    ↓
Git Repository
    |
    ↓
Jenkins Controller
    |
    ↓
Jenkins Agent
    |
    +------> Checkout Code
    |
    +------> Compile
    |
    +------> Test
    |
    +------> SonarQube Analysis
    |
    +------> Package
    |
    ↓
Artifact
```

---

# 25. Jenkins Controller + Agents + SonarQube

Complete architecture:

```text
                         Developer
                             |
                             ↓
                        Git Repository
                             |
                             ↓
                   Jenkins Controller
                             |
                 ┌───────────┴───────────┐
                 ↓                       ↓
              Agent 1                 Agent 2
                 |                       |
                 ↓                       ↓
             Build/Test              Build/Test
                 |
                 ↓
          SonarQube Analysis
                 |
                 ↓
            SonarQube Server
                 |
                 ↓
           Quality Analysis
                 |
                 ↓
              Artifact
```

---

# 26. Example Pipeline Structure

A Jenkins pipeline can be organized into stages such as:

```text
Pipeline
   |
   ├── Checkout
   |
   ├── Compile
   |
   ├── Test
   |
   ├── SonarQube Analysis
   |
   └── Package
```

Example structure:

```groovy
pipeline {

    agent {
        label 'slave1'
    }

    stages {

        stage('Checkout') {
            steps {
                // Checkout source code
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Run SonarQube analysis
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
    }
}
```

The exact SonarQube configuration depends on the scanner and Jenkins environment being used.

---

# 27. Controller vs Agent

| Component          | Purpose                                     |
| ------------------ | ------------------------------------------- |
| Jenkins Controller | Manages and schedules Jenkins workloads     |
| Jenkins Agent      | Executes assigned builds                    |
| Executor           | Provides a build execution slot             |
| Label              | Identifies an agent                         |
| SSH                | Can be used for controller-agent connection |
| Workspace          | Directory used by a build                   |

---

# 28. Important Concepts Learned

Through this practice, I learned:

```text
✓ Jenkins Controller
✓ Jenkins Agents
✓ Controller-Agent Architecture
✓ Jenkins Pipeline Agents
✓ EC2 Agent Configuration
✓ SSH-Based Agent Connection
✓ Executors
✓ Labels
✓ Jenkins Credentials
✓ Agent Monitoring
✓ SonarQube
✓ Code Quality Analysis
✓ SonarQube Scanner
✓ Quality Gates
✓ Jenkins + SonarQube Integration
```

---

# 29. Security Best Practices

During Jenkins and SonarQube configuration, sensitive information should be protected.

Never commit the following to GitHub:

```text
❌ SSH Private Keys
❌ Jenkins Passwords
❌ SonarQube Tokens
❌ AWS Access Keys
❌ Server Credentials
❌ Private Server Details
```

Use secure mechanisms such as:

```text
Jenkins Credentials
GitHub Secrets
AWS IAM Roles
Environment Variables
```

---

# 30. Key Learning

The main concept I learned is that Jenkins can separate **build management** from **build execution**.

```text
Jenkins Controller
       |
       | Manages
       ↓
Jenkins Agents
       |
       | Execute
       ↓
Build / Test / Package
```

SonarQube can then be integrated into the CI pipeline:

```text
Code
 ↓
Build
 ↓
Test
 ↓
SonarQube Analysis
 ↓
Package
```

---

# 31. Overall DevOps Architecture

```text
                         Developer
                             |
                             ↓
                           Git
                             |
                             ↓
                          GitHub
                             |
                             ↓
                   Jenkins Controller
                             |
                ┌────────────┴────────────┐
                ↓                         ↓
            Jenkins Agent 1           Jenkins Agent 2
                |
                ↓
        ┌───────┼────────┐
        ↓       ↓        ↓
     Compile   Test   SonarQube
                       Analysis
                          |
                          ↓
                    SonarQube Server
                          |
                          ↓
                       Package
                          |
                          ↓
                       Artifact
```

---

# 32. Learning Outcome

This hands-on project helped me understand how Jenkins can:

```text
Distribute Build Workloads
          ↓
Use Multiple Agents
          ↓
Execute CI Pipelines
          ↓
Analyze Code with SonarQube
          ↓
Generate Build Artifacts
```

It also helped me understand the importance of:

```text
Jenkins Architecture
+
AWS EC2
+
SSH
+
Maven
+
Git
+
SonarQube
+
CI/CD
```

---

# 33. My DevOps Learning Journey

```text
Git
 ↓
GitHub
 ↓
Maven
 ↓
Jenkins
 ↓
Jenkins Agents
 ↓
SonarQube
 ↓
CI/CD
 ↓
Automation
```

**Learn → Practice → Troubleshoot → Automate → Improve 🚀**
