# Jenkins Distributed Build Infrastructure - Troubleshooting Guide

This document contains all the issues encountered during the implementation of the Jenkins Distributed Build Infrastructure, along with their investigation process, root cause analysis, and final resolution.

---

# Issue 1 - Worker Connected but Immediately Went Offline

## Problem

The Jenkins Worker successfully authenticated using SSH but immediately went offline after connecting.

---

## Symptoms

- Agent connected successfully.
- "Bring this node back online" button was visible.
- Jenkins automatically marked the worker offline.

---

## Initial Assumption

Initially, it appeared that the SSH configuration was incorrect.

Possible causes considered:

- Incorrect SSH keys
- Wrong username
- Invalid private key
- Network issue
- Firewall issue

---

## Investigation Steps

### Step 1 - Verify SSH Manually

Command

```bash
ssh jenkins@172.31.15.60
```

### Result

Passwordless SSH login worked successfully.

✅ SSH configuration was correct.

---

### Step 2 - Verify Java

Command

```bash
java -version
```

Result

OpenJDK 21 detected.

✅ Java was installed correctly.

---

### Step 3 - Verify Maven

Command

```bash
mvn -version
```

Result

Apache Maven 3.9.12

✅ Maven installed.

---

### Step 4 - Verify Git

Command

```bash
git --version
```

Result

Git 2.53

✅ Git installed.

---

### Step 5 - Review Jenkins Logs

Command

```bash
docker logs jenkins --tail 100 | grep -Ei "worker1|offline|disk|temp|swap|monitor"
```

Actual Output

```
Making worker1 offline temporarily due to the lack of disk space
```

---

## Root Cause

The issue was **not related to SSH**.

Ubuntu 26.04 mounts `/tmp` as a `tmpfs` filesystem with approximately **476 MiB** available.

Jenkins Node Monitor expected at least **1 GiB** of temporary space.

Because the threshold was not met, Jenkins automatically marked the worker offline.

---

## Resolution

Navigate to:

```
Manage Jenkins
    ↓
Nodes
    ↓
Configure Monitors
```

Enable:

- ✅ Don't mark agents temporarily offline (Free Temp Space)
- ✅ Don't mark agents temporarily offline (Free Swap Space)

Save the configuration.

---

## Result

The worker remained online.

The Declarative Pipeline executed successfully.

---

## Lesson Learned

Do not assume that an offline Jenkins agent is always caused by SSH problems.

Always:

1. Verify SSH manually.
2. Verify Java.
3. Verify Maven.
4. Verify Git.
5. Review Jenkins logs.
6. Check Node Monitor configuration.

---

# Issue 2 - Controller Disk Warning

## Problem

Jenkins displayed:

```
/var/jenkins_home is almost full
```

---

## Cause

The Docker volume storing Jenkins Home had limited free space.

---

## Status

Not critical for this project.

Scheduled for cleanup in a future module.

---

# Frequently Asked Questions

## Why didn't we use the ubuntu user?

Although Jenkins can connect using the default Ubuntu user, a dedicated `jenkins` service account was created to follow the Principle of Least Privilege.

Benefits:

- Better security
- Easier auditing
- Independent SSH keys
- Service isolation
- Production best practice

---

## Why was Java installed on the Worker?

Because build execution happens on the Worker, not on the Controller.

The Jenkins Controller only schedules jobs.

---

## Why install Maven on the Worker?

Maven compiles the application during pipeline execution.

Since builds run on the Worker, Maven must be installed there.

---

## Why install Git on the Worker?

The pipeline clones repositories directly on the Worker.

Without Git, the checkout stage would fail.

---

## Why create a dedicated Workspace?

```
/home/jenkins/agent
```

This isolates Jenkins build files from the operating system and keeps build artifacts organized.

---

## Why use SSH Keys instead of Password Authentication?

SSH Keys are:

- More secure
- Easier to automate
- Industry standard
- Faster
- Recommended by Jenkins

---

## Why execute builds on the Worker instead of the Controller?

Keeping the Controller free from build execution:

- Improves scalability
- Improves security
- Keeps the UI responsive
- Allows multiple workers in the future

---

## Interview Questions

### Q1. Why did the worker go offline even though SSH was working?

Because Jenkins Node Monitor automatically marked it offline due to the temporary disk space threshold.

---

### Q2. How did you identify the root cause?

By reviewing Jenkins logs and observing:

```
Making worker1 offline temporarily due to the lack of disk space
```

---

### Q3. What was the final solution?

Configured Node Monitors to stop automatically marking agents offline for Temp Space and Swap Space.

---

## Summary

This project involved several real-world troubleshooting scenarios including:

- SSH authentication validation
- Jenkins agent connectivity
- Java verification
- Maven verification
- Git verification
- Node Monitor investigation
- Temporary disk threshold analysis
- Successful pipeline validation

These issues closely resemble production troubleshooting scenarios and significantly improved understanding of Jenkins distributed architecture.