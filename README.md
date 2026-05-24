# Systems Debugging Framework

> **A structured methodology for isolating faults across network, application, system, and database layers.**

[![Markdown](https://img.shields.io/badge/Markdown-000000?style=for-the-badge&logo=Markdown&logoColor=FFFFFF)](https://daringfireball.net/projects/markdown/)&nbsp;[![Linux](https://img.shields.io/badge/Linux-222222?style=for-the-badge&logo=Linux&logoColor=FCC624)](https://kernel.org)

## Overview

Provides repeatable triage checklists, an incident response lifecycle model, and structured documentation for diagnosing complex system failures. Designed for support engineers who need to systematically narrow down root causes before escalating.

## Structure

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

## Usage

1. **During an incident:** Open the relevant checklist and work through it top-to-bottom.
2. **For escalations:** Attach completed checklist output to the Jira/Linear ticket as evidence of investigation steps taken.
3. **For onboarding:** New support engineers use these checklists to build consistent diagnostic habits.

---
Maintained by Harrison Vance — Technical Support & Operations