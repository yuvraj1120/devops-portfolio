# Jenkins Distributed Build Architecture

---

# Overview

This document explains the architecture of the Jenkins Distributed Build Infrastructure implemented during this project.

The infrastructure follows the same design principle used in enterprise environments where the Jenkins Controller is responsible only for orchestration, while build execution is delegated to dedicated Worker nodes.

This architecture provides:

- Better scalability
- Improved security
- Higher availability
- Better resource utilization
- Separation of responsibilities

---

# High-Level Architecture

```text
                    Developer
                        │
                        │
             Git Push / Build Trigger
                        │
                        ▼
        +--------------------------------+
        |      Jenkins Controller        |
        |--------------------------------|
        | Ubuntu EC2                     |
        | Docker                         |
        | Jenkins LTS                    |
        | Build Scheduler                |
        | Pipeline Engine                |
        +--------------------------------+
                        │
                        │ SSH Authentication
                        │
                        ▼
        +--------------------------------+
        |       Jenkins Worker           |
        |--------------------------------|
        | Ubuntu EC2                     |
        | Java 21                        |
        | Maven                          |
        | Git                            |
        | Build Workspace                |
        +--------------------------------+
                        │
                        ▼
             Build / Test / Package
```

---

# Infrastructure Components

## 1. Developer Machine

Purpose

- Access Jenkins UI
- Manage GitHub repositories
- Configure infrastructure
- Trigger builds

---

## 2. Jenkins Controller

Responsibilities

- Store Jenkins configuration
- Manage credentials
- Schedule jobs
- Execute Pipelines
- Assign jobs to agents
- Store build history

The Controller **does not perform actual builds**.

---

## 3. Jenkins Worker

Responsibilities

- Execute build jobs
- Clone Git repositories
- Compile source code
- Run Maven
- Execute tests
- Produce artifacts

---

# Why Separate Controller and Worker?

A single Jenkins server can execute builds directly.

However, production environments rarely use this design.

Instead:

Controller

↓

Schedules Jobs

↓

Worker

↓

Executes Builds

Advantages

- Better performance
- Better scalability
- Better security
- Easy maintenance
- Dedicated build resources

---

# Communication Flow

```text
Developer

↓

Jenkins Dashboard

↓

Pipeline Trigger

↓

Controller

↓

SSH Authentication

↓

Worker

↓

Pipeline Execution

↓

Console Output

↓

Success
```

---

# SSH Authentication Flow

The Worker was configured using SSH authentication.

```text
Controller

Private Key

──────────────►

Worker

authorized_keys
```

Authentication Process

1. Jenkins initiates SSH connection.
2. SSH validates the private key.
3. Worker authenticates the Jenkins user.
4. Secure communication established.
5. Jenkins copies remoting.jar.
6. Agent process starts.
7. Worker comes online.

---

# Jenkins Remoting

When the Worker connects successfully, Jenkins automatically copies:

```
remoting.jar
```

This JAR enables communication between:

Controller

↓

Worker

It is responsible for:

- Command execution
- Console logs
- Workspace management
- File transfer
- Build communication

---

# Workspace Architecture

Each Jenkins job executes inside its own workspace.

Example

```text
/home/jenkins/agent/workspace/pipeline_project1
```

Workspace contains

- Source Code
- Maven Target Directory
- Build Artifacts
- Temporary Files

Every Pipeline gets an isolated workspace.

---

# Pipeline Execution Flow

```text
Pipeline Trigger

↓

Controller

↓

Find Node Label

↓

worker1

↓

SSH

↓

Workspace Creation

↓

Run Shell Commands

↓

Collect Logs

↓

Pipeline Success
```

---

# Security Architecture

```text
Developer

↓

HTTPS

↓

Jenkins Controller

↓

SSH Keys

↓

Worker
```

Security Principles

- Passwordless Authentication
- Dedicated Jenkins User
- Non-root Execution
- Private Key Authentication

---

# Network Architecture

```text
AWS VPC

│

├── Controller EC2
│      Private IP
│
└── Worker EC2
       Private IP
```

Communication between Controller and Worker uses **private IP addresses**, avoiding unnecessary public network traffic.

---

# Label Architecture

Node Label

```text
worker1 linux java maven
```

Labels allow Jenkins to schedule jobs only on machines that satisfy specific requirements.

Example

```groovy
agent {
    label 'worker1'
}
```

Future examples

```text
linux

windows

docker

kubernetes

production

qa
```

---

# Build Execution Lifecycle

```text
Build Trigger

↓

Allocate Node

↓

Connect Worker

↓

Create Workspace

↓

Checkout Source

↓

Execute Build

↓

Generate Logs

↓

Archive Artifacts

↓

Release Workspace
```

---

# Actual Environment

Controller

| Component | Value |
|----------|-------|
| Platform | AWS EC2 |
| OS | Ubuntu |
| Jenkins | Docker |
| Java | OpenJDK 21 |

Worker

| Component | Value |
|----------|-------|
| Platform | AWS EC2 |
| OS | Ubuntu 26.04 |
| Java | OpenJDK 21 |
| Maven | 3.9.12 |
| Git | 2.53 |
| User | jenkins |

---

# Validation Performed

The following components were verified successfully.

- Java Installation
- Maven Installation
- Git Installation
- SSH Connectivity
- Passwordless Authentication
- Jenkins Agent Connection
- Workspace Creation
- Environment Verification Pipeline

---

# Lessons Learned

This implementation demonstrates several production concepts:

- Distributed Jenkins Architecture
- Dedicated Build Workers
- SSH-based Agent Communication
- Workspace Isolation
- Controller Responsibilities
- Agent Responsibilities
- Secure Authentication
- Infrastructure Validation

---

# Future Architecture

The infrastructure will be expanded as follows.

```text
GitHub

↓

Webhook

↓

Jenkins Controller

↓

Worker

↓

Maven

↓

Docker

↓

Docker Hub

↓

Kubernetes

↓

AWS Deployment
```

This architecture will evolve into a complete CI/CD platform.
