# LAB_STATE.md

> **Purpose:** Current authoritative state of the cybersecurity enterprise sandbox.
>
> **Last Updated:** 2026-08-22
>
> **Important:** This document records the state that has actually been built or verified. Planned work must not be represented as completed.

---

# 1. Lab Objective

The lab is a personal enterprise cybersecurity sandbox designed to support:

* SC-200 preparation
* KQL practice
* Microsoft Sentinel practice
* Microsoft Defender/XDR practice
* Active Directory security
* Windows endpoint monitoring
* Security event investigation
* Hybrid/on-premises Azure integration
* Portfolio documentation

The environment is intentionally smaller than a production enterprise environment while modeling the major components of a modern SOC.

---

2. Physical Infrastructure

The lab currently consists of two Proxmox virtualization nodes with different roles.

Proxmox Node #1 — Security Operations / SIEM Node
Component	Current State
Chassis	HP Slimline Desktop
CPU	Intel Core i7-3770
Original CPU	Intel Pentium G2020
RAM	16 GB DDR3
Original RAM	4 GB
Storage	500 GB SSD
Hypervisor	Proxmox VE
Primary Role	Security Operations / SIEM
Status	Operational
Purpose

This node was originally intended to host the entire enterprise sandbox.

Resource constraints demonstrated that running OPNsense, Wazuh, and multiple target VMs on a single 16 GB system would create resource bottlenecks.

The architecture was therefore re-scoped.

The node now serves primarily as the dedicated Security Operations / SIEM node.

Virtual Infrastructure

The primary documented workload on this node is:

Ubuntu Server VM
Wazuh
Wazuh management/SIEM functionality
Proxmox Node #2 — Enterprise Infrastructure Node
Component	Current State
Chassis	Dell OptiPlex 7060 SFF
CPU	Intel Core i7-8700
RAM	32 GB
Primary Storage	500 GB NVMe
Additional Storage	1 TB NVMe via PCIe adapter
Network	Dual-port 1 Gb NIC
Hypervisor	Proxmox VE
Primary Role	Enterprise Infrastructure
Status	Operational

The dual-port NIC originally acquired for the first system was reused in this node.

Virtual Infrastructure

This node currently hosts:

OPNsense
Windows Server 2025
Windows 11 Enterprise

---

# 3. Network

## Firewall

| Component | Current State                        |
| --------- | ------------------------------------ |
| Firewall  | OPNsense                             |
| Role      | Network edge/firewall                |
| Status    | Operational / baseline configuration |

Network troubleshooting has included Windows firewall and Group Policy investigation.

The lab has previously investigated whether domain Group Policy or Windows Defender Firewall was preventing ICMP/ping traffic.

Do not assume that an inability to ping a host indicates that the host or network is unavailable.

---

# 4. Virtual Machines

## Windows Server 2025

| Property | State                              |
| -------- | ---------------------------------- |
| OS       | Windows Server 2025 Evaluation     |
| Role     | Active Directory Domain Controller |
| AD DS    | Completed                          |
| Domain   | `TGR.ad.lab`                       |
| Status   | Operational                        |

The server is part of the lab's identity infrastructure.

---

## Windows 11 Enterprise

| Property           | State                            |
| ------------------ | -------------------------------- |
| OS                 | Windows 11 Enterprise Evaluation |
| Role               | Endpoint / telemetry target      |
| Azure Arc          | Installed                        |
| Security telemetry | Configured/in use                |
| Status             | Operational                      |

The Windows 11 VM is used as a primary endpoint for security monitoring and SC-200 practice.

---

## Kali Linux

Kali Linux is used for security training and course exercises.

Current training environment:

* Kali Linux Rolling 2026.2
* Running through VirtualBox on the laptop
* Network mode previously configured as Bridged

Kali is part of the training workflow but is not the primary Proxmox enterprise infrastructure.

---

5. Wazuh

Wazuh is hosted separately from the Windows/OPNsense infrastructure.

Wazuh Host
Property	State
Proxmox Node	Node #1 — HP Slimline
Guest OS	Ubuntu Server
Security Platform	Wazuh
Role	SIEM / endpoint security monitoring
Status	Implemented / In Use

Wazuh agents have been installed on:

Windows Server 2025
Windows 11 Enterprise

The purpose of the Wazuh deployment is to provide practical experience with:

Windows security events
Authentication activity
Endpoint events
Security alerts
Log analysis
Detection concepts
Investigation workflows

Wazuh is intentionally maintained as a separate security-monitoring platform from the Microsoft security stack.

This allows the lab to compare and work with multiple security monitoring technologies rather than relying exclusively on Microsoft tooling.
---

# 6. Azure Infrastructure

## Log Analytics

| Property  | State                             |
| --------- | --------------------------------- |
| Workspace | `TGR-SC200-LAW`                   |
| Region    | East US                           |
| Purpose   | Security telemetry / KQL practice |
| Status    | Operational                       |

The workspace is actively receiving security telemetry.

---

## Azure Arc

Azure Arc has been installed on the Windows endpoint infrastructure.

Purpose:

* Hybrid Azure integration
* Azure management
* Security/monitoring integration
* Foundation for Microsoft security tooling

Status:

**Implemented**

---

# 7. Telemetry

Security telemetry has been successfully observed in Log Analytics.

The `SecurityEvent` table is available in the current workspace.

Example validation that has returned data:

```kql
SecurityEvent
| where TimeGenerated > ago(24h)
```

The lab has successfully observed Windows security events including, among others:

* 4624 — Successful logon
* 4625 — Failed logon
* 4672 — Special privileges assigned
* 4688 — Process creation
* 4798 — User's local group membership enumerated
* 4799 — Security-enabled local group membership enumerated
* 4907 — Auditing settings changed
* 5058 — Key file operation

Observed event counts have varied over time and should not be treated as permanent state.

---

# 8. Current SecurityEvent Schema Notes


Observed fields include:

```text
SourceSystem
TimeGenerated
Source
EventLog
Computer
EventLevel
EventLevelName
ParameterXml
EventData
EventID
RenderedDescription
AzureDeploymentID
Role
EventCategory
UserName
Message
ManagementGroupName
Type
_IsBillable
_BilledSize
_ResourceId
_SubscriptionId
```

Older SC-200 training examples may not exactly match the current workspace schema. The current SecurityEvent table includes process-related fields such as Process, ProcessName, CommandLine, and ParentProcessName. When practicing KQL, the actual workspace schema takes precedence over older training examples.

When practicing KQL, the actual workspace schema takes precedence over older training examples.

SecurityEvent schema verified on 2026-08-22; detailed field inventory maintained in docs/telemetry/SECURITYEVENT_SCHEMA.md

---

# 9. KQL Practice

KQL practice is an active part of the lab.

Completed/practiced investigation areas include:

### Failed logons

```kql
SecurityEvent
| where TimeGenerated > ago(3d)
| where EventID == 4625
| summarize Count=count()
    by Computer, Account, Hour=bin(TimeGenerated, 1h)
| where Count >= 1
| order by Count desc
```

### Recent security events

```kql
SecurityEvent
| where TimeGenerated > ago(24h)
```

### Event ID analysis

```kql
SecurityEvent
| summarize Count=count() by EventID
| order by Count desc
```

KQL should be treated as an investigation skill rather than simply a collection of memorized queries.

---

# 10. Microsoft Sentinel

Microsoft Sentinel has been activated using the Log Analytics workspace.

| Component                    | State                       |
| ---------------------------- | --------------------------- |
| Sentinel                     | Activated                   |
| Workspace                    | `TGR-SC200-LAW`             |
| Telemetry available          | Yes                         |
| KQL queries                  | Working                     |
| Analytics/detection practice | In progress                 |
| Defender integration         | In progress                 |
| Status                       | Active training environment |

Sentinel should be treated as an active learning environment rather than a production SIEM.

---

# 11. Microsoft Defender

Microsoft Defender services are part of the current/future security stack.

The lab has explored:

* Microsoft Defender XDR
* Microsoft Defender for Endpoint
* Microsoft Defender for Cloud
* Defender CSPM
* Defender alerts
* Defender/Sentinel integration

Current state:

**In Progress / Needs Verification**

Do not mark endpoint onboarding or full XDR integration as completed until the endpoint and portal telemetry have been verified.

---

# 12. Licensing / Trials

The lab has used Microsoft trial/licensing resources for training.

Known resources include:

* Microsoft 365 E5 trial
* Entra-related licensing
* Defender-related licensing/trials
* Microsoft Sentinel trial/free-period functionality
* Defender CSPM

Current licensing should be verified directly in the Azure/Microsoft portals before making cost assumptions.

---

# 13. Cost Monitoring

Azure cost is an explicit lab concern.

Log Analytics ingestion has been monitored.

A recent observation showed approximately:

**430 MB of ingestion over the previous 7 days**

This is an observation at that point in time, not a permanent ingestion rate.

The lab should avoid unnecessary high-volume ingestion.

Before enabling additional telemetry:

1. Identify what data will be collected.
2. Determine whether it is billable.
3. Estimate the potential ingestion impact.
4. Enable only what is needed for the current learning objective.
5. Monitor actual usage afterward.

---

# 14. Known Troubleshooting History

## Windows Firewall / Ping

The lab investigated whether Group Policy was preventing ping/ICMP traffic.

The following were reviewed:

* Domain Group Policy
* Lab computer baseline GPO
* Default Domain Policy
* Windows Defender Firewall
* Inbound firewall rules
* File and Printer Sharing rules

Some relevant firewall rules were disabled, while the examined GPO firewall configuration was not explicitly enforcing the suspected block.

Conclusion:

Do not assume Group Policy is responsible for connectivity problems without further evidence.

---

## SecurityEvent Schema Differences

Older KQL examples from training material have not always matched the current `SecurityEvent` schema.

This is an expected training issue and should be documented rather than treated as a failure of the lab.

---

## Failed Logon Testing

The lab did not initially contain enough naturally occurring failed logons to demonstrate brute-force detection.

A query was therefore adjusted to show available 4625 events rather than requiring an artificial threshold such as ten failures.

This distinction should be preserved:

**Available telemetry ≠ simulated attack activity.**

---

# 15. Project Milestones

## Part 1 — Hardware Upgrades & Proxmox Foundation

**Status: Completed**

Includes:

* Hardware upgrades
* Proxmox VE
* Initial virtualization foundation
* OPNsense

---

## Part 2 — Infrastructure Expansion, Active Directory & Wazuh

**Status: Completed**

Includes:

* Windows Server
* AD DS
* Windows 11
* Wazuh
* Initial enterprise-style infrastructure

---

## Part 3 — Azure Arc & Telemetry Ingestion

**Status: Completed**

Includes:

* Azure Arc
* Log Analytics
* Windows security telemetry
* SecurityEvent data
* Initial KQL investigation

---

## Part 4 — Microsoft Defender / XDR

**Status: In Progress**

Focus:

* Defender XDR
* Defender for Endpoint
* Defender for Cloud
* Endpoint telemetry
* Alert investigation

---

## Part 5 — Sentinel Integration

**Status: In Progress**

Focus:

* Sentinel configuration
* Connectors
* Analytics rules
* Incidents
* Defender integration

---

## Part 6 — KQL / Detection Engineering

**Status: In Progress**

Focus:

* Authentication analysis
* Failed logons
* Privileged activity
* Process creation
* Account activity
* Correlation
* Detection logic
* Investigation workflows

---

# 16. Next Known Objectives

The next objectives should be completed in a controlled sequence rather than all at once.

* [ ] Verify Microsoft Defender XDR configuration
* [ ] Verify Defender for Endpoint availability and endpoint onboarding
* [ ] Verify endpoint telemetry in the Defender portal
* [ ] Continue Defender for Cloud configuration where useful
* [ ] Connect relevant Defender telemetry to Sentinel
* [ ] Build/test Sentinel analytics rules
* [ ] Generate or safely simulate useful security events
* [ ] Investigate resulting alerts/incidents
* [ ] Continue SC-200-oriented KQL practice
* [ ] Document each verified milestone
* [ ] Monitor Azure ingestion/cost impact

---

# 17. State Update Rules

When a change is made:

1. Verify it.
2. Update the relevant section above.
3. Add a dated entry to `CHANGELOG.md`.
4. Record failures or reversals when they are useful for understanding the lab.

Never remove historical information merely because the configuration changed.

The current state belongs here.

The history belongs in `CHANGELOG.md`.
