# Jenkins Distributed Build Infrastructure - Implementation Guide

---

# Overview

This document describes the complete implementation of the Jenkins Distributed Build Infrastructure.

The implementation was performed manually to understand every component involved in a production Jenkins environment including:

- Infrastructure
- SSH Authentication
- Jenkins Agent Communication
- Declarative Pipelines
- Troubleshooting

---

# Environment

## Controller

| Property | Value |
|----------|-------|
| Platform | AWS EC2 |
| Operating System | Ubuntu |
| Jenkins | Docker |
| Purpose | Controller |

---

## Worker

| Property | Value |
|----------|-------|
| Platform | AWS EC2 |
| Operating System | Ubuntu 26.04 |
| Purpose | Build Agent |

---

# Phase 1

# Jenkins Controller Verification

Before configuring the worker node, the controller environment was validated.

---

## Verify Docker Container

Command

```bash
docker ps
```

Purpose

Verify Jenkins container is running.

Result

PASS

---

## Verify Jenkins Image

Command

```bash
docker inspect jenkins | grep Image
```

Purpose

Confirm Jenkins LTS image.

Result

PASS

---

## Verify Restart Policy

Command

```bash
docker inspect jenkins | grep RestartPolicy -A 3
```

Purpose

Ensure container automatically starts after reboot.

Configured Policy

```text
unless-stopped
```

---

## Verify Jenkins Volume

Command

```bash
docker inspect jenkins | grep Source -A 2
```

Purpose

Verify persistent Jenkins Home volume.

Verified

```text
/var/lib/docker/volumes/jenkins_home/_data
```

---

# Phase 2

# Worker Provisioning

A dedicated Ubuntu EC2 instance was launched.

Purpose

Separate build execution from Jenkins Controller.

---

## Update Packages

Command

```bash
sudo apt update
```

Status

Completed Successfully.

---

## Verify Java

Command

```bash
java -version
```

Actual Result

```text
OpenJDK 21
```

Purpose

Required for Jenkins Agent.

---

## Verify Git

Command

```bash
git --version
```

Actual Result

```text
Git 2.53
```

---

## Verify Maven

Command

```bash
mvn -version
```

Actual Result

```text
Apache Maven 3.9.12
```

---

## Verify SSH Service

Command

```bash
sudo systemctl status ssh
```

Initially

Inactive

Resolution

```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

Result

Active

---

# Phase 3

# Jenkins User Creation

A dedicated Jenkins user was created.

Purpose

Avoid executing builds as root.

---

Command

```bash
sudo adduser jenkins
```

---

Add User to Group

```bash
sudo usermod -aG users jenkins
```

---

Verification

```bash
id jenkins
```

Result

PASS

---

# Phase 4

# SSH Authentication

Passwordless SSH authentication was configured.

---

Generate SSH Keys

Worker

```bash
ssh-keygen -t ed25519
```

---

Controller

```bash
ssh-keygen -t ed25519
```

---

Display Public Key

```bash
cat ~/.ssh/id_ed25519.pub
```

---

Configure Authorized Keys

Worker

```bash
nano ~/.ssh/authorized_keys
```

Controller public key was copied.

---

Permissions

```bash
chmod 700 ~/.ssh

chmod 600 ~/.ssh/authorized_keys
```

---

Verify Known Hosts

Controller

```bash
ssh-keyscan <Worker Private IP> >> ~/.ssh/known_hosts
```

---

Manual SSH Test

Controller

```bash
ssh jenkins@<Worker Private IP>
```

Result

Passwordless Login Successful.

This step verified SSH independently before configuring Jenkins.

---

# Phase 5

# Jenkins Agent Configuration

A new Permanent Agent was created.

---

Configuration

Node Name

```text
worker1
```

Remote Workspace

```text
/home/jenkins/agent
```

Labels

```text
worker1 linux java maven
```

Launch Method

```text
Launch agents via SSH
```

Authentication

SSH Username with Private Key

---

# Phase 6

# Worker Connection

After configuration Jenkins automatically performed:

- SSH Authentication
- Workspace Creation
- remoting.jar Transfer
- Agent Startup

Log Highlights

```
Remote root directory does not exist

Creating Workspace

Copying remoting.jar

Starting Agent

Agent Successfully Connected
```

Worker successfully came online.

---

# Phase 7

# Monitoring Issue

Issue

Worker was immediately marked Offline.

Symptoms

```
Making worker1 offline temporarily
```

Investigation

Verified

- SSH
- Java
- Maven
- Git
- Passwordless Login

All components were functioning correctly.

---

Root Cause

Ubuntu 26.04 mounts:

```text
/tmp
```

as

```text
tmpfs
```

with approximately

```text
476 MB
```

Jenkins default monitor expected

```text
1 GB
```

Temporary Space.

---

Resolution

Enabled

```
Don't mark agents temporarily offline
```

for

- Free Temp Space

- Free Swap Space

---

Result

Worker remained Online.

---

# Phase 8

# Environment Verification Pipeline

A Declarative Pipeline was created.

Pipeline verified:

- Hostname

- Current User

- Workspace

- Java

- Maven

- Git

- Disk

- Memory

---

Execution

Pipeline successfully executed on:

```text
worker1
```

Workspace

```text
/home/jenkins/agent/workspace/pipeline_project1
```

Final Result

```text
Finished: SUCCESS
```

---

# Final Validation

The following validations were completed successfully.

✅ Docker

✅ Jenkins

✅ Worker

✅ SSH Authentication

✅ Passwordless Login

✅ Java

✅ Maven

✅ Git

✅ Jenkins Agent

✅ Workspace

✅ Declarative Pipeline

---

# Lessons Learned

- Always verify SSH manually before Jenkins.
- Never execute builds as root.
- Separate Controller and Worker.
- Validate Java before connecting agents.
- Jenkins automatically copies remoting.jar.
- Ubuntu 26.04 uses tmpfs for /tmp.
- Jenkins Node Monitors may incorrectly mark nodes offline depending on threshold configuration.
- Distributed builds are significantly more scalable than Controller-only execution.

---

# Conclusion

A complete production-style Jenkins Distributed Build Infrastructure was successfully implemented on AWS.

The Controller is responsible only for orchestration while build execution is delegated to a dedicated Linux Worker using secure SSH authentication.

This implementation forms the foundation for future CI/CD projects including:

- GitHub Integration
- Maven Builds
- Docker
- Kubernetes
- Terraform
- AWS Deployments
