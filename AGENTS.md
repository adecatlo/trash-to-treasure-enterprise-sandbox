# AGENTS.md

## Scope and Authority

This file is the governing instruction file for all work performed in this repository. It defines documentation authority, state handling, verification requirements, change-management expectations, Git restrictions, live-infrastructure authorization, and security rules.

If another repository document conflicts with these instructions, identify the conflict rather than silently choosing a source or rewriting the conflicting material.

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
   * **Needs Verification**
   * **Broken**
   * **Removed**
3. Do not represent planned work as completed.
4. Do not assume that a service is configured merely because it exists in the Azure portal.
5. Do not assume telemetry is working merely because an agent or connector is installed.
6. When evidence is insufficient, mark the state as `Needs Verification` rather than guessing.
7. Identify conflicts between documents rather than silently resolving them.

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

Do not maintain a duplicate current-architecture snapshot in this instruction file. The exact current architecture and state of every lab component must be taken from `LAB_STATE.md`.

If another document describes a different architecture, determine whether it is a historical phase narrative, clearly outdated current-state documentation, or a true unresolved conflict. Preserve historical context and report unresolved conflicts.

---

## Document Roles and Authority

### `LAB_STATE.md` — Current Verified State

`LAB_STATE.md` is the authoritative source for the lab's current verified state. Current architecture, operational status, integrations, telemetry, and component state must be taken from this file.

Do not copy a claim into `LAB_STATE.md` merely because it appears in another document. A current-state claim requires sufficient verification under the verification rules below.

### `CHANGELOG.md` — Chronological History

`CHANGELOG.md` preserves dated, chronological records of meaningful verified changes, failures, reversals, and architectural decisions. It must not be treated as the current-state authority.

Historical entries should not be rewritten merely because the lab later changed. Corrections should be explicit and preserve the original chronology.

### `README.md` — Public-Facing Overview

`README.md` is the public-facing project overview. It should explain the lab's purpose, architecture at a high level, major milestones, and links to supporting documentation.

It may summarize current state, but it must not override `LAB_STATE.md`. Avoid detailed operational claims in the README when they cannot be kept current or safely published.

### Phase Documents Under `docs/` — Historical Phase Narrative

Phase documents preserve the state, decisions, learning process, and narrative of their respective phases. They are historical records, not competing current-state authorities.

Do not silently rewrite a phase document to make its historical status, next steps, or decisions match the current lab. If clarification is needed, prefer an explicit dated note, retrospective annotation, or link to `LAB_STATE.md` while preserving the original narrative.

Detailed reference documents under `docs/` that are intended to describe current configuration should clearly say so and include a verification or update date.

---

## Documentation Conflicts

When documents disagree:

1. Identify the exact conflicting claims and their locations.
2. Determine whether the difference reflects historical progression, stale current-state documentation, missing evidence, or a true unresolved contradiction.
3. Use `LAB_STATE.md` for current verified state, but do not silently rewrite another document.
4. If the authoritative state itself is unclear, mark the matter **Needs Verification**.
5. Ask for confirmation when available evidence cannot resolve a material contradiction.
6. Preserve useful historical context when documenting the resolution.

---

## Documentation Change Management

When verified lab changes are being documented, consider whether both of the following require updates:

* `LAB_STATE.md`, if the change affects current verified state.
* `CHANGELOG.md`, if the change is historically meaningful.

Update `README.md` only when the public overview, architecture summary, or milestone presentation materially changes.

Do not automatically rewrite phase documents to reflect later state. Preserve their historical narrative.

Do not update current-state documentation merely because a command was attempted, a resource was created, or an intended configuration was described. Record only what the available evidence supports.

If a requested documentation update lacks sufficient evidence, use **Needs Verification** rather than presenting the claim as verified.

Before changing documentation:

1. Read `LAB_STATE.md`.
2. Read the relevant `CHANGELOG.md` entries.
3. Read the affected public, phase, or reference documentation.
4. Identify whether each material claim is current, historical, planned, or unverified.
5. Report material conflicts and assumptions.

After making changes, summarize what changed and why. When a commit is requested, provide that summary for review before committing unless the user explicitly directs otherwise.

---

## Verification Standard

A configuration should be considered **Completed** only when there is evidence that it works.

Use these evidence categories:

**User-verified fact**

* An explicit current observation or test result reported by the user.
* May be documented as verified state when the user clearly identifies what was observed or tested and the result.
* Record the verification date and the reported evidence when available.
* Do not broaden the claim beyond what the user actually observed.

**Agent-verified fact**

* A current observation or test result directly inspected by the agent through authorized read-only checks or an explicitly authorized test.
* Record the verification method, date, and result when useful.

**Inference**

* A conclusion suggested by available information but not directly observed or tested.
* Must be identified as an inference and must not be recorded as verified state.

**Needs Verification**

* The configuration may exist or a claim may be plausible, but neither a qualifying user-verified fact nor agent-verified evidence establishes the current result.

Installation, resource existence, licensing, configuration attempts, portal presence, or unsupported claims in another document are not sufficient by themselves to establish **Completed** status.

Evidence may include successful queries, service or agent health, portal visibility, configuration output, event timestamps, test results, screenshots, or other reproducible observations appropriate to the component.

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

## Live Infrastructure Authorization

Repository access, documentation review, and permission to edit repository files do not authorize changes to live lab infrastructure.

Do not make changes to Azure, Microsoft 365, Microsoft Entra, Defender, Sentinel, Proxmox, OPNsense, Active Directory, endpoints, virtual machines, networking, agents, connectors, cloud resources, or any other live lab system unless the user explicitly requests the live-infrastructure change.

Authorization to document, recommend, review, or troubleshoot a change is not authorization to implement it. Read-only inspection may be used when it is within the user's request and available authorization, but any action that changes live state requires an explicit request.

Live-infrastructure authorization does not imply authorization to make unrelated changes. Keep all actions within the exact requested scope, explain material risk or cost, prefer reversible changes, and verify the result.

---

## Azure CLI and Infrastructure-as-Code Safety

Azure CLI, Azure PowerShell, Bicep, Terraform, or other infrastructure tooling may be used to inspect or manage the lab only within the authorization boundaries defined in this file.

### Default Azure Access

Azure operations are **read-only by default**.

Repository access, terminal access, Azure authentication, and authorization to modify infrastructure are separate permissions. Access to one does not imply permission for another.

Before performing Azure inspection or administration:

1. Run `az account show`.
2. Confirm the active Azure subscription and tenant context.
3. Do not switch subscriptions or tenants without explicit user approval.
4. Keep operations within the user's requested scope.

Read-only inspection commands may be used when they are relevant to the user's request and do not alter live state.

### Azure Changes Require Explicit Approval

Do not execute an Azure operation that creates, modifies, deletes, deploys, enables, disables, onboards, offboards, assigns permissions, changes data collection, or otherwise alters live state unless the user explicitly approves that operation.

This includes changes involving:

* Resource groups
* Azure Arc
* Azure Monitor Agent
* Data Collection Rules
* Log Analytics workspaces
* Microsoft Sentinel
* Microsoft Defender products
* Microsoft Entra ID
* RBAC assignments
* Policies
* Networking
* Virtual machines
* Extensions
* Diagnostic settings
* Connectors
* Analytics or detection rules
* Telemetry collection

Authorization for one Azure change does not authorize additional related changes.

Before an authorized change, explain the expected effect, material security implications, likely cost implications, and rollback path when applicable.

### Infrastructure as Code

Infrastructure-as-Code files may be written or modified without authorization to deploy them, provided the requested repository work itself is authorized.

Creating or editing Bicep, Terraform, ARM templates, scripts, or deployment commands does not grant permission to execute them against Azure.

For Bicep or ARM deployments:

* Prefer validation before deployment.
* Use Azure deployment `what-if` before an authorized deployment when supported.
* Review the proposed changes before applying them.
* Do not proceed when `what-if` reveals unexpected resource changes.

For Terraform:

* Review `terraform plan` before an authorized apply.
* Do not run `terraform apply` or `terraform destroy` without explicit approval.
* Treat Terraform state files as potentially sensitive and do not commit them unless the repository has an intentional, secure state-management design.

### Azure Credentials and Local Artifacts

Never commit Azure authentication material or local Azure CLI state.

Do not add the following to Git:

* Azure CLI authentication caches
* Access tokens
* Refresh tokens
* Service-principal credentials
* Client secrets
* Certificates containing private keys
* `.env` files containing credentials
* Terraform state files containing sensitive infrastructure data
* Exported portal or CLI output containing sensitive identifiers or credentials

Authentication should use supported Azure authentication mechanisms rather than credentials stored in repository files.

### Public Repository Sanitization

Treat this repository as publicly readable.

Before committing Azure-derived documentation, command output, screenshots, scripts, configuration, or examples, review them for information that should not be publicly disclosed.

Sanitize or replace with placeholders when appropriate:

* Subscription IDs
* Tenant IDs
* User email addresses
* Object IDs
* Client IDs
* Resource IDs
* Sensitive hostnames
* Public or private IP addresses when disclosure is unnecessary
* Internal domain or infrastructure details when disclosure would create unnecessary risk
* Authentication or session information

Do not sanitize information in a way that makes technical documentation misleading. Preserve the technical meaning while removing unnecessary identifying values.

### Azure Cost Control

Any operation that can materially increase Azure consumption requires cost consideration before execution.

Examples include:

* Enabling additional telemetry
* Expanding Data Collection Rules
* Increasing Log Analytics ingestion
* Increasing retention
* Enabling additional Defender capabilities
* Creating continuously running resources
* Deploying additional compute
* Enabling services with consumption-based pricing

Before such a change, identify the likely source of cost and obtain explicit user approval for the live change.

### Verification After Authorized Changes

An Azure command completing successfully does not by itself establish that the intended configuration works.

After an authorized Azure change:

1. Verify the resulting Azure resource state.
2. Verify the intended functional result when practical.
3. Verify telemetry or security functionality separately when relevant.
4. Record only what the evidence establishes.
5. Update `LAB_STATE.md` and `CHANGELOG.md` only when the documentation rules in this file justify doing so.

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

## Sensitive Information and Public Documentation

Never place credentials or sensitive values in repository documentation or other tracked files. This includes:

* Passwords
* API keys
* Access or refresh tokens
* Client secrets
* Private keys
* Recovery codes
* Session identifiers
* Authentication cookies
* Credential-bearing connection strings
* Personal account secrets or unnecessary personal information
* Infrastructure, account, tenant, subscription, resource, host, or network details when disclosure would create a security or privacy risk

Use placeholders when documenting commands.

Example:

```text
<DOMAIN_ADMIN_USERNAME>
<AZURE_SUBSCRIPTION_ID>
<CLIENT_ID>
```

Never commit `.env` files containing secrets.

Treat `README.md` and portfolio-oriented documentation as public by default. Check examples, screenshots, commands, output, queries, and logs for secrets or unnecessarily sensitive identifiers before adding them.

If sensitive material is discovered, do not reproduce it in summaries or patches. Identify its location and recommend appropriate remediation without exposing the value.

---

## Git Operations

Do not create commits, amend commits, push branches, publish tags, open pull requests, or otherwise publish Git changes unless the user explicitly requests the specific action.

Permission to edit repository files does not imply permission to commit or push them.

Before any requested commit:

1. Show or summarize the proposed changes for the user's review.
2. Identify the files changed.
3. Report relevant validation performed.
4. Surface unresolved conflicts, assumptions, or **Needs Verification** items.

Do not include unrelated existing user changes in a commit.

Never use destructive Git operations to discard work unless the user explicitly requests them and the exact scope has been confirmed.

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

This section defines the stable purpose of each phase. Current phase status must be taken from `LAB_STATE.md`; historical phase documents under `docs/` must retain their original narrative.

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

An AI agent working in this repository should behave as a careful lab documentarian and an operator only when the user has explicitly authorized the requested operation. Repository work does not grant authority to administer live infrastructure.

The agent should:

* Read the current state before proposing changes.
* Explain why a change is necessary.
* Prefer reversible changes.
* Obtain explicit authorization before live-infrastructure changes.
* Ask before destructive actions.
* Record important changes.
* Distinguish user-verified facts, agent-verified facts, inference, and **Needs Verification**.
* Preserve existing working configuration.
* Avoid unnecessary Azure resources.
* Avoid repeating completed setup steps.
* Use the existing naming conventions.
* Keep documentation synchronized.
* Identify material conflicts rather than silently resolving them.
* Stay within the user's requested scope.

An explicit current observation or test result reported by the user may be treated as a user-verified fact. Document only the state established by that report and do not infer broader completion.

---

## State Labels

Use these labels consistently for current-state documentation. Do not substitute ambiguous terms such as “implemented,” “deployed,” “active,” or “operational” without mapping them to one of these labels or explaining exactly what was verified.

**Completed**

* Implemented and verified through a qualifying user-verified fact or agent-verified fact.

**In Progress**

* Currently being configured or tested.

**Planned**

* Intended future work that has not begun.

**Needs Verification**

* Configuration may exist, but there is insufficient evidence to establish the current result.

**Broken**

* Previously working functionality is currently failing.

**Removed**

* Previously implemented but intentionally removed.

Do not move an item to **Completed** solely because configuration steps were attempted.

---

## Do Not Guess

If information is unavailable, say:

> Needs Verification.

Do not convert an inference into verified state. When relying on a user-verified fact, attribute the observation appropriately and do not extend it beyond the reported test or result.

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
