# Systems Debugging Framework

> **A structured methodology for isolating faults across network, application, system, and database layers.**

[![Markdown](https://www.shieldcn.dev/badge/Markdown-000000.svg?variant=default&logo=Markdown&logoColor=FFFFFF&size=xs)](https://daringfireball.net/projects/markdown/)&nbsp;[![Linux](https://www.shieldcn.dev/badge/Linux-222222.svg?variant=default&logo=Linux&logoColor=FCC624&size=xs)](https://kernel.org)

---

## The Problem

**The Symptom:** During incident response, support engineers jump between ad-hoc commands without a consistent triage methodology. Tribal knowledge dictates who gets called for which type of failure, and diagnostic steps vary between shifts.

**The Investigation:** New engineers spent weeks building muscle memory for basic fault isolation. Even experienced engineers would occasionally skip foundational checks and escalate prematurely, only to have the receiving team confirm the issue was a full disk or a closed firewall port.

**The Resolution:** A structured, layered debugging framework organized into repeatable triage checklists. Each checklist targets a specific failure domain, starts at the outermost layer and works inward, and includes concrete CLI commands for every step. Engineers follow the same methodology every time — regardless of seniority.

---

## Checklists

### API Gateway Triage
Is the outage at the edge or upstream?

```text
External Health Check  →  TLS/SSL Verification  →  Route Resolution
  ↓                        ↓                         ↓
400/500 Range?          Certificate Expiry?       DNS Resolution
```

| Step | Command | What It Reveals |
| ------ | --------- | ----------------- |
| Health check | `curl -I https://<endpoint>` | Upstream reachability & HTTP status |
| TLS handshake | `openssl s_client -connect <host>:443` | Certificate expiry, SNI mismatch |
| Latency breakdown | `curl -w "%{time_total}" -o /dev/null -s` | End-to-end response time |

> **Status:** Work in progress — triage steps being expanded.

### Linux Performance Triage (USE Method)
The **USE Method** (Utilization, Saturation, Errors) is a vendor-agnostic systems analysis framework that works on any resource.

| Layer | Utilization | Saturation | Errors |
| ------- | ------------- | ------------ | -------- |
| **CPU** | `top`, `mpstat -P ALL` | `uptime` (load avg), `vmstat` (`r` vs `b`) | `dmesg` (OOM, watchdog) |
| **Memory** | `free -h` (available) | `vmstat` (`si`/`so` swap activity) | `dmesg` (OOM killer) |
| **Disk** | `iostat -xz` (`%util`) | `iostat` (`avgqu-sz`, `await`) | `dmesg` (I/O errors), `journalctl` |
| **Network** | `ip -s link` (packet stats) | `ss -s` (socket stats) | `netstat -i` (`RX-ERR`/`TX-ERR`) |

Quick snapshot command:
```bash
uptime && dmesg | tail && vmstat 1 5 && mpstat -P ALL 1 5 && iostat -xz 1 5 && free -h
```

### Network Connectivity Triage
A step-by-step guide following the OSI model from the network layer up to the application layer.

| OSI Layer | Check | Command |
| ----------- | ------- | --------- |
| Layer 3 (IP) | Reachability | `ping -c 4 <ip>`, `mtr -rw <host>` |
| Layer 4 (TCP) | Port open | `nc -zv <host> <port>`, `ss -tulpn` |
| Layer 4 (Firewall) | Rules blocking | `iptables -L -n -v` |
| Layer 7 (HTTP) | Application response | `curl -Lv https://<domain>` |
| Layer 7 (TLS) | Certificate validity | `openssl s_client -connect <host>:443` |

**Triage logic:**
1. Source vs Destination — can I reach any other host?
2. Local vs Remote — does it work from localhost of the target?
3. Internal vs External — does it work from within the VPC?

---

## Methodology

All checklists follow the same layered approach:

1. **Start at the outermost layer** — confirm or rule out the broadest possible cause first (e.g., network reachability before application config).
2. **Work inward** — advance one layer at a time. Never skip a layer.
3. **Produce evidence** — every step includes a specific CLI command whose output can be saved as ticket evidence.
4. **Escalate with context** — if the checklist is exhausted without resolution, attach the full output trail to the escalation ticket.

---

## Incident Response Lifecycle

```text
[Detect] → [Triage] → [Diagnose] → [Resolve] → [Validate] → [Postmortem]
```

Refer to [`docs/Incident_Response_Lifecycle.md`](docs/Incident_Response_Lifecycle.md) for the full workflow definition.

> **Status:** Work in progress — lifecycle stages being documented.

---

## Usage

```bash
# During an active incident — start with the relevant checklist:
#   API Gateway issue?     → checklists/api-gateway-triage.md
#   Host is slow?          → checklists/linux-performance-triage.md
#   Can't connect?         → checklists/network-connectivity-triage.md

# Walk through each section top-to-bottom.
# Run every command and save the output.
# If all steps pass without finding the root cause, escalate with the output attached.

git clone https://github.com/h-vance/systems-debugging-framework.git
```

---

## Prerequisites

- **OS:** Linux (all checklists assume standard Linux CLI tools)
- **Required tools:** `ping`, `mtr` / `traceroute`, `nc`, `ss`, `iptables`, `curl`, `openssl`, `top` / `htop`, `vmstat`, `mpstat`, `iostat`, `free`, `dmesg`, `journalctl`, `ip`, `netstat`
- **Python:** Not required (checklists are shell-based)

---

## Repository Structure

```text
.
├── checklists/
│   ├── api-gateway-triage.md           # Step-by-step API gateway fault isolation
│   ├── linux-performance-triage.md     # CPU, memory, disk, and process debugging
│   └── network-connectivity-triage.md  # DNS, TCP, routing, and firewall checks
├── docs/
│   └── Incident_Response_Lifecycle.md  # End-to-end incident handling workflow
├── models/
│   └── refactor_log.md                 # Change tracking for framework updates
└── CONTRIBUTING.md                     # Contribution guidelines
```

---

## Related Repositories

| Repository | Description |
| ---------- | ----------- |
| [**ops-diagnostics**](https://github.com/h-vance/ops-diagnostics) | Python & Bash diagnostic scripts for automated health verification, log analysis, and system profiling |
| [**log-rotation-maintenance**](https://github.com/h-vance/log-rotation-maintenance) | Automated Bash scripts for log rotation, compression, and storage cleanup on Linux servers |
| [**cloud-operations-runbook**](https://github.com/h-vance/cloud-operations-runbook) | 15+ standardized SOPs and runbooks covering compute, networking, identity, and application troubleshooting |

---

Maintained by Harrison Vance — Technical Support & Operations
