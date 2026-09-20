# devsecops-pipeline-demo
---
# Purpose
**A CI/CD pipeline demonstrating shift-left security: automated SAST, dependency, and container scanning with enforced fail conditions.**
---
# Architecture
**push → test → semgrep/trivy/docker-scan (parallel) → gate → merge**
---
# Selected Tools
Tool        | What         | Prevents
semgrep     | Source Code  | Insecure Code, SQL Injection, Hard Coded Credentials
trivy       | Dependencies | Known Vulnerabilities for Dependencies
dependabot  | Dependencies | Unupdated Dependencies
docker-scan | Docker Image | Shows Vulnerabilities in Underlying OS
---
# 

