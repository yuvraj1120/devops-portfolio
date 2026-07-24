# Jenkins Distributed Build Infrastructure on AWS

> **A production-style Jenkins distributed build infrastructure built on AWS using a dedicated Controller and SSH-based Linux Worker.**

---

# Project Overview

This project demonstrates how to build a production-style Jenkins distributed architecture from scratch on AWS.

Instead of executing build jobs directly on the Jenkins Controller, a dedicated Linux Worker (Agent) was provisioned and connected securely using SSH key authentication.

The primary objective of this project was not simply to install Jenkins, but to understand how Jenkins Controller and Agents communicate internally, how SSH authentication works, how build execution is delegated to remote nodes, and how to troubleshoot real-world infrastructure issues.

Unlike a single-node Jenkins setup, this project follows the architecture used in enterprise environments where the Jenkins Controller is responsible only for orchestration while all builds are executed on dedicated worker machines.

---

# Project Objectives

The objectives of this project were:

- Build a dedicated Jenkins Controller on AWS EC2.
- Deploy Jenkins inside Docker.
- Create a separate AWS EC2 instance as Jenkins Worker.
- Configure passwordless SSH authentication.
- Configure Jenkins SSH Agent.
- Execute Declarative Pipelines on Worker instead of Controller.
- Understand Jenkins Remoting.
- Learn production troubleshooting techniques.
- Document the complete implementation.

---

# Architecture

```

                    Developer
                         │
                         │
                  Push / Build
                         │
                         ▼
             +----------------------+
             | Jenkins Controller   |
             | Ubuntu EC2           |
             | Docker               |
             | Jenkins LTS          |
             +----------------------+
                         │
                  SSH Authentication
                         │
                         ▼
             +----------------------+
             | Jenkins Worker       |
             | Ubuntu EC2           |
             | Java 21              |
             | Maven 3.9            |
             | Git                  |
             +----------------------+
                         │
                         ▼
                 Build Execution

```

---

# Technology Stack

| Component | Version |
|------------|---------|
| AWS EC2 | Ubuntu 26.04 LTS |
| Jenkins | LTS (Docker) |
| Docker | Latest Stable |
| Java | OpenJDK 21 |
| Maven | 3.9.12 |
| Git | 2.53 |
| SSH | OpenSSH |
| Pipeline | Declarative Pipeline |

---

# Infrastructure

## Jenkins Controller

| Property | Value |
|----------|-------|
| Platform | AWS EC2 |
| Operating System | Ubuntu 24.04/26.04 |
| Jenkins Installation | Docker Container |
| Purpose | Job Scheduling & Orchestration |

---

## Jenkins Worker

| Property | Value |
|----------|-------|
| Platform | AWS EC2 |
| Operating System | Ubuntu 26.04 |
| Java | OpenJDK 21 |
| Maven | Apache Maven 3.9.12 |
| Git | Installed |
| Authentication | SSH Keys |

---

# Folder Structure

```

05-Jenkins
│
└── 01-SSH-Agent-Setup
│
├── README.md
├── architecture.md
├── implementation.md
├── commands.md
├── troubleshooting.md
├── interview-questions.md
├── notes.md
├── source
└── assets

```

---

# Implementation Summary

The project was implemented in the following phases.

## Phase 1

Provision Jenkins Controller

- AWS EC2 Launch
- Docker Installation
- Jenkins Deployment

---

## Phase 2

Provision Jenkins Worker

- Launch New EC2
- Java Installation
- Maven Installation
- Git Installation
- SSH Server Verification

---

## Phase 3

Configure SSH Authentication

- Create Jenkins User
- Generate SSH Keys
- Configure authorized_keys
- Verify Passwordless SSH Login

---

## Phase 4

Configure Jenkins Agent

- Create Permanent Agent
- Configure Remote Workspace
- Configure Labels
- Configure SSH Credentials
- Connect Agent

---

## Phase 5

Validate Build Environment

A Declarative Pipeline was executed on the Worker Node to verify:

- Java
- Maven
- Git
- Workspace
- Disk
- Memory
- Hostname

The pipeline executed successfully.

---

# Pipeline Workflow

```

Developer

↓

Jenkins Controller

↓

SSH Connection

↓

Worker Node

↓

Environment Verification

↓

Pipeline Success

```

---

# Validation

The following validations were successfully completed.

✅ Worker connected over SSH

✅ Passwordless authentication verified

✅ Jenkins Agent online

✅ Workspace automatically created

✅ Java verified

✅ Maven verified

✅ Git verified

✅ Declarative Pipeline executed successfully

---

# Skills Demonstrated

This project demonstrates practical experience with:

- Jenkins Administration
- Distributed Build Architecture
- Linux Administration
- AWS EC2
- Docker
- SSH Authentication
- Git
- Maven
- Java
- Jenkins Pipelines
- Infrastructure Troubleshooting

---

# Challenges Faced

During implementation several real-world issues were encountered.

Examples include:

- SSH Authentication
- Agent Connection
- Offline Worker
- Jenkins Node Monitoring
- Temporary Disk Threshold
- Controller Disk Space
- Passwordless Authentication
- Maven Availability
- Workspace Verification

Each issue has been documented in the troubleshooting guide.

---

# Lessons Learned

This project helped build practical understanding of:

- Difference between Controller and Worker
- Jenkins Remoting
- SSH Key Authentication
- Dedicated Build Infrastructure
- Workspace Management
- Linux Permissions
- Production Debugging Approach

---

# Future Improvements

The infrastructure will be extended with:

- GitHub Webhooks
- Spring Boot Build
- Unit Testing
- Docker Image Build
- Docker Hub Push
- SonarQube
- Nexus Repository
- Kubernetes Deployment
- Terraform Provisioning
- Slack Notifications

---

# Screenshots

The following screenshots are included inside the assets directory.

- Jenkins Dashboard
- Controller Configuration
- Worker Configuration
- SSH Authentication
- Worker Online
- Declarative Pipeline
- Console Output
- Successful Build

---

# References

Official Documentation

- Jenkins
- Docker
- OpenSSH
- Maven
- Git
- AWS EC2

---

# Author

**Yuvraj Singh**

DevOps Portfolio Project

Built for learning Production Jenkins Architecture on AWS.