# Jenkins Distributed Build Architecture

## Architecture Diagram

                    Developer
                        │
                        ▼
                 GitHub Repository
                        │
                        ▼
              Jenkins Controller
             (Docker Container)
                        │
                SSH Connection
                        │
                        ▼
               Linux Agent (worker1)
                        │
              Maven Build Execution
                        │
                        ▼
                  Build Artifact (.jar)

---

## Components

### Developer

Writes code and pushes changes to GitHub.

---

### GitHub Repository

Stores the source code and Jenkins pipeline.

---

### Jenkins Controller

Responsible for:

- Scheduling jobs
- Managing pipelines
- Delegating builds
- Monitoring agents

The controller **does not perform heavy builds**.

---

### Docker Container

The Jenkins Controller is hosted inside a Docker container rather than directly on the operating system.

Benefits

- Easy upgrades
- Isolation
- Portability
- Persistent storage through Docker volumes

### Linux SSH Agent (worker1)

Responsible for:

- Receiving build requests
- Running Maven
- Executing shell commands
- Returning build results

---



### Maven

Compiles the Java application and creates the final JAR artifact.