<div align="center">

# 🚀 CostPilot

### Real-Time Cloud Cost Monitoring and Resource Optimization System on Azure

[![MIT License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Azure](https://img.shields.io/badge/Platform-Microsoft%20Azure-0078D4?logo=microsoftazure&logoColor=white)](https://azure.microsoft.com)
[![React](https://img.shields.io/badge/Frontend-React.js-61DAFB?logo=react&logoColor=black)](https://reactjs.org)
[![Python](https://img.shields.io/badge/Backend-Python-3776AB?logo=python&logoColor=white)](https://python.org)
[![Serverless](https://img.shields.io/badge/Architecture-Serverless-orange)](https://azure.microsoft.com/en-us/products/functions)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)](https://github.com/features/actions)

*An event-driven system that continuously monitors Azure resource utilization and spending, automatically detects idle "zombie" resources, and triggers user-approved deallocation workflows which aims to keep your cloud environment lean, efficient, and accountable.*

---

**Author:** Tan Jian Ming · [jianmingtan2003@gmail.com](mailto:jianmingtan2003@gmail.com)  
**Institution:** Universiti Teknologi Malaysia (UTM)  
**Project Type:** Final Year Project I (FYP) · Cloud Computing / FinOps

</div>

---

## 🌩️ Problem Background

Cloud computing has revolutionized how organizations provision IT resources through a pay-as-you-go model. However, the same flexibility that makes the cloud powerful also introduces significant cost management challenges:

- **Delayed billing reports** — abnormal spending is only detected *after* charges have already accumulated.
- **Idle or "Zombie" resources** — virtual machines, databases, and other resources left running because no one remembers to shut them down.
- **Manual monitoring** — time-consuming, inconsistent, and prone to human error.
- **Lack of audit trails** — insufficient logging and access control reduce visibility into who is taking actions on resources.

These issues lead to over-provisioned infrastructure, inflated operational expenditure, and unpredictable cloud budgets.

---

## 💡 What is CostPilot?

**CostPilot** is a cloud-native, serverless application built on **Microsoft Azure** that solves these problems through:

| Capability | Description |
|---|---|
| 🔍 **Real-Time Monitoring** | Continuously tracks CPU, memory, network, and disk usage via Azure Monitor |
| 💰 **Cost Alerting** | Detects budget threshold breaches using Azure Cost Management in real time |
| 🤖 **Idle Resource Detection** | Applies a Multi-Metric Rule-Based Algorithm to identify "zombie" resources |
| ✉️ **Human-in-the-Loop Approval** | Sends automated email approval requests before any deallocation is executed |
| ⚡ **Automated Deallocation** | Shuts down or deallocates approved idle resources via Azure Functions |
| 📋 **Audit Logging** | Records every automated and manual action in Azure Cosmos DB |
| 🔐 **Access Control** | Uses Microsoft Entra ID, Managed Identities, and Azure RBAC for secure access |
| 📊 **Dashboard** | Provides a real-time web dashboard hosted on Azure Static Web Apps |

Unlike existing solutions that rely solely on threshold alerts or rigid policy rules, CostPilot merges **monitoring**, **intelligent decision-making**, and **workflow automation** into a single event-driven pipeline.

---

## ✨ Key Features

- **Event-Driven Architecture** — Actions are triggered automatically by real-time telemetry data, not manual checks.
- **Serverless Scalability** — Azure Functions scale automatically with demand, requiring zero infrastructure management.
- **Multi-Metric Idle Detection** — Resources must be below CPU, memory, network, AND disk thresholds for a sustained period before being flagged — preventing false positives from momentary usage bursts.
- **Configurable Policies** — Threshold values (CPU %, idle duration, timeout period) are user-defined and stored in Azure Cosmos DB — no code changes required.
- **Pre-Deployment Cost Estimation** — Estimate costs before resources are provisioned to prevent over-spending from the start.
- **Comprehensive Audit Trail** — Every optimization action, cost saving, and user response is logged for accountability and reporting.
- **Affordable & Accessible** — Designed for students, SMEs, and individual Azure users, not just large enterprises.

---

## 🏗️ System Architecture

CostPilot is deployed as a **cloud-native and serverless application** on Microsoft Azure.

```
┌─────────────────────────────────────────────────────────────────┐
│                     Microsoft Azure                             │
│                                                                 │
│  ┌─────────────────┐         ┌─────────────────────────────┐   │
│  │  Azure Static   │◄────────│   Microsoft Entra ID        │   │
│  │  Web Apps       │         │   (Authentication + RBAC)   │   │
│  │  (React.js UI)  │         └─────────────────────────────┘   │
│  └────────┬────────┘                                           │
│           │                                                     │
│           ▼                                                     │
│  ┌─────────────────┐   trigger   ┌──────────────────────────┐  │
│  │  Azure Monitor  ├────────────►│   Azure Functions        │  │
│  │  (CPU/RAM/Disk) │             │   (Backend Logic &       │  │
│  └─────────────────┘             │    Policy Evaluation)    │  │
│  ┌─────────────────┐   trigger   │                          │  │
│  │  Azure Cost     ├────────────►│                          │  │
│  │  Management     │             └──────────┬───────────────┘  │
│  └─────────────────┘                        │                  │
│                                             │                  │
│                          ┌──────────────────▼───────────────┐  │
│                          │   Azure Logic App                 │  │
│                          │   (Email Approval Workflow)       │  │
│                          └──────────────────┬───────────────┘  │
│                                             │                  │
│                    ┌────────────────────────▼───────────────┐  │
│                    │   Azure Cosmos DB                       │  │
│                    │   (Policies, Logs, Savings Records)     │  │
│                    └────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

**Deployment:** Managed through a **CI/CD pipeline** using GitHub and GitHub Actions, ensuring both frontend and backend components are automatically built and deployed on every push.

---

## 🔄 How It Works (System Flowchart)

![CostPilot System Flowchart](CostPilot%20flowchart.png)

---

## 🧠 Idle Resource Detection Algorithm

CostPilot uses a **Multi-Metric Rule-Based Detection Algorithm** that compares telemetry data from Azure Monitor against user-defined thresholds stored in Azure Cosmos DB.

### Detection Logic (Pseudocode)

```python
# Threshold variables (user-configurable via Cosmos DB policies)
CPU_THRESHOLD     = 5       # %
MEMORY_THRESHOLD  = 10      # %
NETWORK_THRESHOLD = 5       # MB/day
DISK_THRESHOLD    = 10      # IOPS
IDLE_DURATION     = idle_days        # User-defined (days)
APPROVAL_TIMEOUT  = response_time    # Minutes before auto-approve

# --- Multi-metric idle check ---
if (avg_cpu     < CPU_THRESHOLD     and
    avg_memory  < MEMORY_THRESHOLD  and
    avg_network < NETWORK_THRESHOLD and
    avg_disk    < DISK_THRESHOLD    and
    state_duration >= IDLE_DURATION):

    status = "Idle"

    # Trigger human-in-the-loop approval
    trigger_logic_app()
    user_response = get_response(timeout=APPROVAL_TIMEOUT)

    if user_response in ("Yes", "Timeout"):
        deallocate_resource()
        log_savings_to_cosmos_db()
    else:
        maintain_state()    # User declined — resource preserved

# Underutilized: flag for recommendation only
elif 5 <= avg_cpu <= 20:
    status = "Underutilized"
    queue_recommendation()    # Suggest resize, no auto-action

# Active: no action needed
else:
    status = "Active"
```

### Why Multi-Metric?

A resource is only flagged **Idle** if **all four metrics** (CPU, memory, network, disk) are simultaneously below their thresholds for the required duration. This prevents false positives caused by transient spikes or single-metric anomalies, ensuring active workloads are never disrupted.

---

## ☁️ Azure Services Used

| Category | Azure Service | Role |
|---|---|---|
| **Frontend** | Azure Static Web Apps | Hosts the React.js web dashboard |
| **Authentication** | Microsoft Entra ID | User authentication, identity management, and RBAC |
| **Monitoring** | Azure Monitor | Collects CPU, memory, disk, and network telemetry |
| **Cost Analysis** | Azure Cost Management | Tracks spending trends and budget threshold breaches |
| **Execution** | Azure Functions | Serverless backend logic — evaluates policies, triggers actions |
| **Communication** | Azure Logic Apps | Sends automated email approval workflows to users |
| **Storage / Logging** | Azure Cosmos DB | Stores policies, audit logs, optimization records, savings data |

---

## 📊 Performance Evaluation Metrics

CostPilot's effectiveness is evaluated through controlled experiments in an Azure test environment using three key metrics:

### 1. Resource Detection Accuracy (False Positive Rate)

$$FPR = \frac{FP}{FP + TN}$$

- **FP** — Resources incorrectly flagged as idle (active VMs wrongly targeted for deallocation)
- **TN** — Resources correctly identified as active
- **Goal:** FPR as close to **0** as possible, ensuring active workloads are never disrupted.

### 2. System Responsiveness (Latency)

$$Latency = T_{act} - T_{detect}$$

- **T_detect** — Time when idle/threshold breach is detected
- **T_act** — Time when the optimization action is executed
- **Goal:** Minimize end-to-end latency to ensure the system truly operates in real time.

### 3. Cost Reduction Percentage

$$Cost\ Reduction = \frac{C_{bef} - C_{aft}}{C_{bef}} \times 100\%$$

- **C_bef** — Total cloud cost before optimization
- **C_aft** — Total cloud cost after optimization
- **Goal:** Demonstrate measurable, quantifiable financial savings through automated resource management.

---

## 🛠️ Requirements

### Software
| Tool | Purpose |
|---|---|
| Windows 10/11 or macOS | Development environment |
| React.js | Frontend dashboard |
| Python | Azure Functions backend logic |
| Microsoft Azure Portal | Cloud service provisioning |
| VS Code | Primary IDE |
| GitHub + GitHub Actions | Version control and CI/CD |
| Chrome / Edge | Browser for dashboard access |

### Hardware
| Requirement | Minimum Spec |
|---|---|
| Processor | Intel Core i5 or equivalent |
| RAM | 8 GB |
| Storage | 256 GB |
| Connectivity | Reliable internet connection |

### Technology Stack
- **Architecture:** Event-driven, Serverless computing
- **Monitoring:** Real-time cost monitoring via Azure Monitor + Cost Management
- **Algorithm:** Multi-Metric Rule-Based Idle Detection
- **Workflow:** Azure Logic Apps orchestration
- **Network:** DNS, HTTP/HTTPS, SMTP, REST API, CORS
- **Security:** Microsoft Entra ID, Managed Identities, Azure RBAC, Audit Logging

---

## 🔁 CI/CD Pipeline

CostPilot uses **GitHub Actions** for automated deployments:

```
Push to main branch
       │
       ▼
┌──────────────────────────────────────┐
│          GitHub Actions              │
│                                      │
│  ┌──────────────┐  ┌──────────────┐  │
│  │ Build &      │  │ Build &      │  │
│  │ Deploy       │  │ Deploy       │  │
│  │ Frontend     │  │ Backend      │  │
│  │ → Azure      │  │ → Azure      │  │
│  │   Static     │  │   Functions  │  │
│  │   Web Apps   │  │              │  │
│  └──────────────┘  └──────────────┘  │
└──────────────────────────────────────┘
```

Both the React.js frontend and Python Azure Functions backend are automatically built and deployed on every push to the `main` branch.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**CostPilot** — *Keeping your cloud lean, efficient, and accountable.*

Prepared by [Tan Jian Ming](mailto:jianmingtan2003@gmail.com) · Universiti Teknologi Malaysia

</div>
