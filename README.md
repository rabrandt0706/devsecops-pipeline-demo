# DevSecOps Pipeline Demo

A CI/CD pipeline demonstrating **shift-left security** — automated SAST, dependency, and container scanning with enforced fail conditions on a simple Flask application.

---

## 🎯 Purpose

This project shows how security can be built directly into a CI/CD pipeline rather than bolted on afterward. Every push triggers a set of automated scanners that check the code, its dependencies, and its container image — and the pipeline is configured to **fail on severe or critical findings**, not just report them.

---

## 🏗️ Architecture

```
push → test → [semgrep | trivy | docker-scan] (parallel) → gate → merge
```

The three scanners run in parallel rather than sequentially, so the pipeline stays fast even as more security checks are added.

---

## 🧰 Tools & Skills Used

**Core tools**
| Tool | Purpose |
|---|---|
| Git & GitHub | Version control, remote repo, pull requests |
| GitHub Actions | CI/CD automation and workflow orchestration |
| Python & Flask | The sample application being secured |
| pytest | Automated testing, run before any security scan |
| Docker | Containerizing the application |
| Semgrep | SAST — static source code scanning |
| Trivy | SCA — dependency scanning, and container image scanning |
| Dependabot | Automated dependency update PRs |

**Skills demonstrated**
- Writing and reading YAML for GitHub Actions workflows
- Configuring parallel CI jobs
- Shift-left security: catching issues before merge, not after deployment
- SAST vs. SCA vs. container/OS-level scanning — knowing what each layer actually protects against
- Enforcing security gates (fail-the-build conditions) vs. passive reporting
- Reading and triaging vulnerability scan output (CVE severity, fixed vs. unfixed)
- Docker fundamentals: writing a Dockerfile, running as a non-root user, minimizing base image size
- Debugging CI failures by reading GitHub Actions logs

---

## 🔍 Selected Tools & What They Catch

| Tool | Scans | Prevents |
|---|---|---|
| **Semgrep** | Source code | Insecure code patterns, SQL injection, hardcoded credentials |
| **Trivy (SCA)** | `requirements.txt` dependencies | Known CVEs in third-party packages |
| **Dependabot** | Dependencies | Outdated packages — opens automatic update PRs |
| **Trivy (image scan)** | Docker image | Vulnerabilities in the underlying OS and system packages |

---

## 🛠️ Process

1. **Built the sample app** — a minimal Flask app returning `"Hello from the DevSecOps demo app!"`, plus a `test_app.py` file for automated testing.
2. **Created `requirements.txt`** pinning `flask==3.0.3`.
3. **Wrote the initial CI workflow** to install dependencies and run the test suite automatically on every push.
4. **Added Semgrep (SAST)** to scan source code before it merges. This acts as an automated "proofread" of the code itself, catching insecure patterns a human reviewer might miss.
5. **Added Trivy (SCA)** to scan dependencies. For a project at this scale, Trivy was the better fit over Snyk — it's free, requires no account, and integrates in a single workflow step. Snyk offers a deeper, more polished analysis on its paid tier, but that level of depth isn't necessary for a project with only one dependency.
6. **Added Dependabot** via a separate config file (`.github/dependabot.yml`, outside the `workflows` folder) to automate dependency updates. It scans `requirements.txt` on a schedule and opens a pull request automatically when a newer, safer version of a package is available — defending against known vulnerabilities before they're even flagged by a scan.
7. **Created a `Dockerfile`** to containerize the app, then added an image scan so Trivy checks the underlying OS layer, not just the Python dependencies. This step is configured to **fail the pipeline** if a high or critical vulnerability is found in the base image — ensuring nothing ships with a known, unresolved OS-level flaw.

---

## ✅ Results

![CI pipeline showing test, semgrep, and docker-scan passing while trivy fails](https://github.com/user-attachments/assets/61c94da7-1174-4218-b7d8-147284ff354b)

All jobs pass except the Trivy scan — **this is expected behavior**. The security gate is designed to fail the build when a severe or critical vulnerability is detected, and it's doing exactly that.

![Trivy scan output showing 44 known vulnerabilities](https://github.com/user-attachments/assets/a89b1470-7479-4a06-b0ab-cb80d114e433)

This run surfaced 44 known vulnerabilities in the base image, which correctly blocked the push from proceeding.

**Next step:** triage each of the 44 findings individually. Given their severity, they should be addressed — either patched, upgraded, or explicitly documented as accepted risk — before this branch merges to main.

---

## 📈 At a Larger Scale

If this project were scaled up for a real production environment, a few things would change:

- **Add Snyk alongside Trivy.** Snyk's deeper analysis is worth the paid tier at scale, while Trivy remains useful for smaller services with fewer dependencies — using both would balance depth and speed across a larger system.
- **Introduce a ticketing system for findings.** Rather than vulnerabilities living only in a CI log, each finding would be tracked in a system like Jira, giving the team a clear, documented, and assignable record of what needs to be addressed and by whom.

---

## 📂 Repository Structure

```
devsecops-pipeline-demo/
├── .github/
│   ├── workflows/
│   │   └── ci.yml
│   └── dependabot.yml
├── app.py
├── test_app.py
├── requirements.txt
├── Dockerfile
├── .gitignore
└── README.md
```


