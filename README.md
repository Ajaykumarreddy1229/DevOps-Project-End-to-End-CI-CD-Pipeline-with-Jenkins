📌 Project Overview

This project demonstrates an end-to-end CI/CD Pipeline using Jenkins
for a Java web application.

The pipeline automates the software delivery workflow from source code
checkout to application deployment and artifact storage.

🔄 Overall Workflow

GitHub
   ↓
Jenkins
   ↓
Compile
   ↓
Test
   ↓
Package WAR
   ↓
Upload Artifact to S3
   ↓
Deploy to Apache Tomcat

🏗️ Architecture

                    ┌─────────────────┐
                    │     GitHub      │
                    │  Source Code    │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │     Jenkins     │
                    │   CI/CD Server  │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              ▼              ▼              ▼
        ┌──────────┐   ┌──────────┐   ┌─────────────┐
        │  Maven   │   │   Test   │   │  Pipeline   │
        │  Build   │   │  Stage   │   │ Automation  │
        └────┬─────┘   └────┬─────┘   └─────────────┘
             │              │
             └──────┬───────┘
                    ▼
             ┌─────────────┐
             │   WAR File  │
             │  Artifact   │
             └──────┬──────┘
                    │
             ┌──────┴─────────┐
             ▼                ▼
      ┌─────────────┐  ┌─────────────┐
      │   Tomcat    │  │  Amazon S3  │
      │  Deployment │  │   Storage   │
      └─────────────┘  └─────────────┘

🛠️ Technologies Used

Technology      Purpose

GitHub          Source Code Management
Jenkins         CI/CD Automation
Groovy          Jenkins Pipeline as Code
Maven           Build and Testing
Apache Tomcat   Application Deployment
AWS EC2         Server Infrastructure
Amazon S3       Build Artifact Storage

📋 Prerequisites

Before starting the project, the following are required:

AWS account

Amazon Linux EC2 instance

Jenkins server

Java

Maven

Git/GitHub repository

Apache Tomcat server

Amazon S3 bucket

Required Jenkins plugins and credentials

🔧 Tomcat Setup

A separate Amazon Linux server is used for Apache Tomcat deployment.

Install Java

sudo dnf install java-21-amazon-corretto -y

Download Tomcat

wget https://dlcdn.apache.org/tomcat/tomcat-11/v11.0.26/bin/apache-tomcat-11.0.26.tar.gz

Extract Tomcat

tar -zxvf apache-tomcat-11.0.26.tar.gz

Start Tomcat

cd apache-tomcat-11.0.26/bin
sh startup.sh

Tomcat can then be accessed through:

http://<EC2-IP>:8080/

🔌 Jenkins Plugins

The project uses Jenkins plugins to integrate the different stages of
the CI/CD workflow.

Important plugins/concepts practiced:

Deploy to Container

S3 Publisher

Pipeline: AWS Steps

Blue Ocean

Git integration

Maven integration

🔐 Jenkins Credentials

Credentials are configured in Jenkins for connecting to external
services.

Examples include:

Tomcat username/password credentials

AWS credentials for S3

GitHub repository access when required

Security Note: Never commit passwords, AWS access keys, secret
keys, or other credentials directly into the repository.

🚦 Jenkins Pipeline

The pipeline follows the standard CI/CD flow:

CODE → BUILD → TEST → ARTIFACT → DEPLOYMENT

Example Pipeline

pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/<your-username>/<your-repository>.git'
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

        stage('Artifact') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploy WAR file to Tomcat'
            }
        }
    }
}

The deployment stage can be configured using Jenkins' Deploy to
Container functionality and Tomcat credentials.

📦 Maven Commands

Compile

mvn compile

Test

mvn test

Package

mvn clean package

The package stage generates the WAR file used for deployment.

☁️ Upload Artifacts to Amazon S3

The generated WAR file can also be stored in Amazon S3.

Example workflow:

Jenkins
   ↓
Build WAR
   ↓
WAR Artifact
   ↓
Amazon S3 Bucket

This provides centralized storage for build artifacts.

The project notes use the AWS region:

ap-south-1

Configure your own bucket and Jenkins AWS credentials rather than
committing credentials to the repository.

🚀 Deployment to Tomcat

After the WAR file is generated, Jenkins can deploy it to the Tomcat
server.

Example deployment configuration:

WAR/EAR Files: **/*.war
Context Path: myapp
Container: Tomcat

After successful deployment, the application can be accessed using:

http://<tomcat-server-ip>:8080/myapp

🎛️ Jenkins Parameters

The project also covers Jenkins parameters for passing input to jobs.

Examples:

Choice --- select one option

String --- provide text input

Multi-line String --- provide multiple lines

File --- upload a file

Boolean --- yes/no type input

Example environment choices:

Dev
Test
Prod

⏸️ Manual Approval

A manual input step can be used before deployment.

Example:

Build
  ↓
Test
  ↓
Artifact
  ↓
Manual Approval
  ↓
Deployment

This allows a user to verify the pipeline input before continuing with
deployment.

📊 Pipeline Stages

The project demonstrates both single-stage and multi-stage Jenkins
pipelines.

A multi-stage pipeline can separate:

Checkout

Compile

Test

Code Review / Quality Analysis

Artifact Creation

Upload to S3

Deployment

📚 What I Learned

Through this project, I practiced:

Creating Jenkins Pipelines

Writing Pipeline as Code using Groovy

Connecting Jenkins with GitHub

Building Java applications using Maven

Running automated tests

Creating WAR artifacts

Deploying applications to Apache Tomcat

Configuring Jenkins credentials

Using Jenkins plugins

Uploading artifacts to Amazon S3

Using Jenkins parameters

Adding manual approval steps

Understanding the complete CI/CD workflow

🎯 Key Takeaway

This project helped me understand how multiple DevOps tools can work
together to automate software delivery.

The main concept I practiced was:

Source Code
    ↓
Build
    ↓
Test
    ↓
Artifact
    ↓
Store
    ↓
Deploy

🔮 Future Improvements

Add SonarQube code-quality analysis

Add automated notifications

Add GitHub Webhook trigger

Add Docker containerization

Deploy using Kubernetes

Add Infrastructure as Code with Terraform

Implement separate Dev/Test/Prod environments
