# Jenkins SSH Agent Implementation

## Step 1 - Launch AWS EC2 Instance

A new Ubuntu EC2 instance was created to host the Jenkins Controller and Linux SSH Agent.

### Configuration

- Ubuntu LTS
- t2.micro
- Security Group
- Port 22
- Port 8080
- Port 50000

---

## Validation

Verified SSH connectivity from the local machine.