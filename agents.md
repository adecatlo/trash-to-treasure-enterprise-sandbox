# AGENTS.md

## Purpose

This repository documents and manages a personal cybersecurity enterprise sandbox used for:

* Microsoft SC-200 preparation
* KQL practice
* Security monitoring and detection engineering
* Microsoft Sentinel practice
* Microsoft Defender/XDR practice
* Active Directory security
* Windows endpoint telemetry
* Hybrid/on-premises to Azure integration
* SOC investigation workflows

This is a learning and portfolio lab, not a production environment.

The repository documents the lab as it actually exists. Do not invent completed configuration, telemetry, detections, incidents, or security controls.

---

## Core Rule: Preserve Lab Reality

`LAB_STATE.md` is the authoritative source for the current state of the lab.

Before making recommendations or changing documentation:

1. Read `LAB_STATE.md`.
2. Determine whether the requested component is:

   * **Completed**
   * **In Progress**
   * **Planned**
   * **Broken / Needs Investigation**
   * **Removed / Replaced**
3. Do not represent planned work as completed.
4. Do not assume that a service is configured merely because it exists in the Azure portal.
5. Do not assume telemetry is working merely because an agent or connector is installed.
6. When uncertain, mark the state as `Needs Verification` rather than guessing.

---

## Lab Philosophy

This lab is intentionally built incrementally.

The goal is to understand how enterprise security infrastructure fits together rather than simply deploying as many Microsoft services as possible.

Prefer:

* Small, testable changes
* Documented configuration
* Repeatable procedures
* Verification after changes
* KQL-based validation
* Clear separation between planned and completed work
* Cost-conscious Azure usage
* Evidence-based conclusions

Avoid unnecessary production-scale architecture.

---

## Current Lab Architecture

The physical virtualization host is a Dell OptiPlex 7060 SFF with:

* Intel Core i7-8700
* 32 GB RAM
* 500 GB NVMe
* Additional 1 TB NVMe SSD
* Proxmox VE

The network edge uses OPNsense.

Primary virtual infrastructure includes:

* Windows Server 2025 Evaluation Edition

  * Active Directory Domain Services
  * Domain Controller
  * Domain: `TGR.ad.lab`
* Windows 11 Enterprise Evaluation

  * Security telemetry target
  * Azure Arc connected
* Wazuh

  * Security monitoring/SIEM component
* Kali Linux

  * Training/security tooling environment
  * Running separately from the Proxmox lab on the user's laptop through VirtualBox

Azure/Microsoft security infrastructure includes:

* Azure Log Analytics Workspace: `TGR-SC200-LAW`
* Azure Arc
* Microsoft Sentinel
* Microsoft Defender-related services
* Microsoft Entra services
* Microsoft 365 E5 trial
* Defender CSPM components

The exact current state of each component must be taken from `LAB_STATE.md`.

---

## Documentation Hierarchy

Use the repository documentation in this order:

1. `LAB_STATE.md`

   * Current truth
2. `CHANGELOG.md`

   * Historical changes
3. `README.md`

   * Human-readable project narrative
4. Other documentation

   * Detailed implementation notes, tutorials, screenshots, queries, etc.

The README may describe the project roadmap, but it must not override the actual state recorded in `LAB_STATE.md`.

---

## Change Management

Whenever a meaningful lab configuration change is made:

1. Make the change.
2. Verify the change.
3. Update `LAB_STATE.md`.
4. Add an entry to `CHANGELOG.md`.
5. Update the README only when the project narrative or milestone status has materially changed.

Do not update documentation merely because a command was attempted.

Documentation should describe successful, verified changes.

---

## Verification Standard

A configuration should be considered **Completed** only when there is evidence that it works.

Examples:

### Azure telemetry

Do not mark telemetry ingestion complete solely because:

* Azure Arc is installed
* A Data Collection Rule exists
* A connector exists
* An agent reports as installed

Prefer evidence such as:

```kql
SecurityEvent
| where TimeGenerated > ago(24h)
| take 10
```

or another query demonstrating that expected data is actually present.

### Detection rules

A detection should not be marked completed merely because the analytics rule exists.

Verify:

* The rule is enabled
* The query executes
* Relevant data exists
* The expected alert/incident behavior occurs

### Defender

Do not mark Defender for Endpoint/XDR functionality as completed until the endpoint enrollment, sensor/agent state, telemetry, and portal visibility have been verified.

---

## KQL Rules

KQL is a major purpose of this lab.

When writing or troubleshooting KQL:

* Prefer the schema currently available in the workspace.
* Do not blindly reuse older tutorials.
* Verify column names before assuming they exist.
* Clearly distinguish between legacy and current schemas.

The current `SecurityEvent` data observed in this lab includes fields such as:

* `TimeGenerated`
* `Computer`
* `EventID`
* `EventLog`
* `EventLevel`
* `EventLevelName`
* `EventData`
* `RenderedDescription`
* `UserName`
* `Message`
* `Source`
* `Type`
* `_IsBillable`
* `_BilledSize`
* `_ResourceId`
* `_SubscriptionId`

Older examples may reference columns that are not present in the current environment.

When a query fails because of schema differences, troubleshoot the actual workspace schema rather than forcing the old query to work.

---

## Cost Awareness

Azure usage must be treated as potentially billable.

Before recommending a configuration that could materially increase ingestion or other Azure consumption:

* Identify the likely billable component.
* Explain what generates the cost.
* Prefer limited test windows where appropriate.
* Avoid unnecessary high-volume telemetry.
* Use the Azure cost/usage information when available.

The lab is intended for learning and portfolio development, not continuous enterprise-scale ingestion.

The Log Analytics workspace has previously shown measurable ingestion, so ingestion should be monitored rather than assumed to be negligible.

---

## Security and Credentials

Never place the following in repository files:

* Passwords
* API keys
* Access tokens
* Client secrets
* Private keys
* Recovery codes
* Connection strings containing credentials
* Personal account secrets

Use placeholders when documenting commands.

Example:

```text
<DOMAIN_ADMIN_USERNAME>
<AZURE_SUBSCRIPTION_ID>
<CLIENT_ID>
```

Never commit `.env` files containing secrets.

---

## Troubleshooting Method

When troubleshooting the lab:

1. Identify the exact symptom.
2. Identify the layer involved.
3. Verify the local configuration.
4. Verify network connectivity.
5. Verify identity/authentication.
6. Verify agent/service health.
7. Verify Azure resource configuration.
8. Verify telemetry.
9. Verify the expected result.
10. Document the resolution.

Do not repeatedly change unrelated settings without establishing which layer is failing.

---

## Lab Phases

The project has evolved through several major phases.

### Part 1 — Hardware and Proxmox Foundation

* Hardware upgrades
* Proxmox VE deployment
* Initial virtualization foundation
* OPNsense network edge

### Part 2 — Infrastructure Expansion

* Windows Server deployment
* Active Directory Domain Services
* Windows 11 endpoint
* Wazuh deployment/telemetry
* Initial enterprise-style lab architecture

### Part 3 — Azure Arc and Telemetry

* Azure Arc integration
* Azure Log Analytics
* Windows telemetry ingestion
* KQL investigation
* SecurityEvent analysis

### Part 4 — Microsoft Defender

Planned/ongoing work includes:

* Defender XDR
* Defender for Endpoint
* Defender for Cloud
* Endpoint telemetry
* Security alerts and investigations

The exact status must be taken from `LAB_STATE.md`.

### Part 5 — Microsoft Sentinel Integration

Work includes:

* Sentinel deployment
* Data connectors
* Analytics
* Incidents
* Defender/Sentinel integration

The exact status must be taken from `LAB_STATE.md`.

### Part 6 — KQL and Detection Engineering

Focus areas include:

* Authentication events
* Failed logons
* Successful logons
* Privileged activity
* Process creation
* Account activity
* Event correlation
* Detection logic
* Investigation queries

---

## AI Agent Behavior

An AI agent working in this repository should behave as a lab operator/documentarian, not as an autonomous production administrator.

The agent should:

* Read the current state before proposing changes.
* Explain why a change is necessary.
* Prefer reversible changes.
* Ask before destructive actions.
* Record important changes.
* Distinguish observed facts from assumptions.
* Preserve existing working configuration.
* Avoid unnecessary Azure resources.
* Avoid repeating completed setup steps.
* Use the existing naming conventions.
* Keep documentation synchronized.

When the user says something has been completed, update the state only after enough evidence exists to document what was actually completed.

---

## State Labels

Use these labels consistently:

**Completed**

* Implemented and verified.

**In Progress**

* Currently being configured or tested.

**Planned**

* Intended future work that has not begun.

**Needs Verification**

* Configuration may exist, but there is insufficient evidence that it works.

**Broken**

* Previously working functionality is currently failing.

**Removed**

* Previously implemented but intentionally removed.

---

## Do Not Guess

If information is unavailable, say:

> Needs verification.

Do not fabricate:

* Resource IDs
* Subscription details
* IP addresses
* Azure pricing
* Connector status
* Agent status
* Detection results
* Event counts
* Security incidents
* Configuration values

The goal of this repository is to preserve an accurate record of the lab.
