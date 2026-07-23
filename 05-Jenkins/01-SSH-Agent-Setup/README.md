# Jenkins Distributed Build Using SSH Agent

## Project Overview

This project demonstrates how to configure a distributed Jenkins environment where the Jenkins Controller delegates build execution to a dedicated Linux SSH Agent.

The controller is deployed inside a Docker container running on an AWS EC2 Ubuntu instance. Instead of executing builds on the controller, a remote Linux node (worker1) performs all build activities through secure SSH communication.

The project covers the complete implementation, troubleshooting process, and validation of a production-style Jenkins distributed build architecture.