# LAB_STATE.md

> **Purpose:** Current authoritative state of the cybersecurity enterprise sandbox.
>
> **Last Updated:** 2026-09-24
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

# 2. Physical Infrastructure

The lab currently consists of two Proxmox virtualization nodes with different roles.

## Proxmox Node 1 — Secondary Security / Lab Compute

| Component | Current State |
| --- | --- |
| Host/display name | `Wazuh-host` |
| Chassis | HP Slimline Desktop |
| CPU | Intel Core i7-3770 |
| Original CPU | Intel Pentium G2020 |
| RAM | 16 GB DDR3 |
| Original RAM | 4 GB |
| Storage | 500 GB SSD |
| Hypervisor | Proxmox VE |
| Current role | Secondary security and controlled-test compute |
| Status | Operational |

This node was originally intended to host the entire enterprise sandbox. Resource testing showed that running OPNsense, Wazuh, and multiple target VMs on one 16 GB system would create resource bottlenecks, so the architecture was re-scoped.

The node is no longer described solely as an actively running Wazuh/SIEM node. It currently preserves the Wazuh platform while also hosting the Kali attack/test system used for controlled security exercises.

| VM ID | Proxmox display name | Guest identity | Current role/state |
| --- | --- | --- | --- |
| 100 | `ubuntu-siem` | Ubuntu Server / Wazuh | Wazuh VM retained; currently powered off/idle while the learning focus is the Microsoft security stack. |
| 101 | `Kali-Attack-Box` | Sauron / `eye-of-sauron` | Controlled attack/test machine; used for exercises including SSH failed-password testing against gandalf-the-white. |

## Proxmox Node 2 — Primary Enterprise Infrastructure

| Component | Current State |
| --- | --- |
| Chassis | Dell OptiPlex 7060 SFF |
| CPU | Intel Core i7-8700 |
| RAM | 32 GB |
| Primary storage | 500 GB NVMe |
| Additional storage | 1 TB NVMe via PCIe adapter |
| Network | Dual-port 1 Gb NIC |
| Hypervisor | Proxmox VE |
| Current role | Primary enterprise infrastructure |
| Status | Operational |

The dual-port NIC originally acquired for the first system was reused in this node.

Current verified VM inventory:

| VM ID | Proxmox display name | Guest identity/hostname | Role |
| --- | --- | --- | --- |
| 100 | `OPNsense` | OPNsense | Firewall/router |
| 101 | `TGR-ADDC2025` | `TGR-ADDS` | Windows Server 2025 domain controller |
| 102 | `Azure-VM` | `TGR-BBaggins` | Windows 11 Enterprise endpoint |
| 103 | `TGR-Linux-VM` | `gandalf-the-white` | Ubuntu/Linux security endpoint |

Proxmox display names and guest hostnames are intentionally recorded separately; they are not interchangeable resource names.

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

| Property | State |
| --- | --- |
| Proxmox display name | `TGR-ADDC2025` |
| Guest hostname | `TGR-ADDS` |
| OS | Windows Server 2025 Evaluation |
| Role | Active Directory Domain Controller |
| AD DS | Completed |
| Domain | `TGR.ad.lab` |
| Status | Operational |

The server is part of the lab's identity infrastructure.

---

## Windows 11 Enterprise

| Property | State |
| --- | --- |
| Proxmox display name | `Azure-VM` |
| Guest hostname | `TGR-BBaggins` |
| OS | Windows 11 Enterprise Evaluation |
| Role | Endpoint / telemetry target |
| Azure Arc | Connected |
| Security telemetry | Verified through Windows Security Events via AMA |
| Status | Operational |

The Windows 11 VM is used as a primary endpoint for security monitoring and SC-200 practice.

---

## Ubuntu / Linux Security Endpoint

| Property | State |
| --- | --- |
| Proxmox display name | `TGR-Linux-VM` |
| Guest hostname | `gandalf-the-white` |
| OS | Ubuntu 24.04.3 LTS |
| Role | Linux security telemetry target |
| Azure Arc | Connected |
| Syslog via AMA | Completed / verified |
| Defender device inventory | Present / verified |
| Status | Operational |

---

## Kali Linux

Kali is the lab's controlled attack/test system.

| Property | State |
| --- | --- |
| Proxmox node | `Wazuh-host` |
| VM ID | 101 |
| Proxmox display name | `Kali-Attack-Box` |
| Guest identity | Sauron / `eye-of-sauron` |
| Role | Controlled attack and telemetry-generation system |
| Status | Operational for controlled exercises |

The Kali VM has been used to generate controlled security activity, including SSH failed-password testing against gandalf-the-white. The earlier VirtualBox-based Kali environment is historical context and is not the current primary Kali architecture.

---

# 5. Wazuh

Wazuh is hosted separately from the Windows/OPNsense infrastructure.

| Property | State |
| --- | --- |
| Proxmox node | Node 1 — `Wazuh-host` |
| VM ID / display name | 100 / `ubuntu-siem` |
| Guest OS | Ubuntu Server |
| Security platform | Wazuh |
| Role | SIEM / endpoint security monitoring platform retained for lab use |
| Current state | Powered off / idle |

Wazuh has not been removed or abandoned. Its VM and prior deployment are retained, but it is not currently the active learning focus; current work is centered on the Microsoft security stack.

Historically, Wazuh agents were installed on the Windows Server 2025 and Windows 11 systems for Windows security-event, authentication, endpoint, alert, log-analysis, detection, and investigation practice.

Wazuh remains available as a separate security-monitoring platform for future comparison with Microsoft tooling.
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

Azure Arc is implemented across the three current monitored servers/endpoints.

| Azure Arc machine | Operating system | Arc agent status | Resource group | Monitoring extension |
| --- | --- | --- | --- | --- |
| `gandalf-the-white` | Ubuntu 24.04.3 LTS | Connected | `TGR-SecurityLab-RG` | Installed |
| `TGR-ADDS` | Windows Server 2025 | Connected | `TGR-SecurityLab-RG` | Installed |
| `TGR-BBaggins` | Windows 11 Enterprise | Connected | `TGR-SecurityLab-RG` | Installed |

**Status: Completed / verified**

The Azure Arc view verifies connection and extension state. Its Defender-extension column is not, by itself, evidence of Microsoft Defender for Endpoint onboarding; Defender device presence is recorded separately below from the Microsoft Defender inventory.

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

## September 2026 Collection State

The current collection state is based on the supplied portal and query evidence:

| Component | State | Evidence |
| --- | --- | --- |
| Windows Security Events via AMA | **Completed** | `TGR-ADDS` and `TGR-BBaggins` use separate DCRs and have verified `SecurityEvent` ingestion. |
| `TGR-ADDS` event scope | **Completed** | `TGR-ADDS-Security-DCR` remains associated with the Azure Arc resource and uses custom XPath filters containing a deliberately curated set of Security Event IDs. A 30-day query returned 5,613 records, with the most recent event on September 24, 2026 local time / September 25 UTC. |
| `TGR-BBaggins` event scope | **Completed** | The Windows 11 endpoint uses the Common event collection scope. Recreating its DCR association restored `SecurityEvent` ingestion. |
| DCR synchronization root cause | **Needs Verification** | Recreating the association restored ingestion, but the proposed synchronization explanation remains a working hypothesis. |
| Azure Activity ingestion | **Completed** | A successful `AzureActivity` query returned Azure control-plane records, including SecurityInsights data-connector operations, policy actions, Logic App/connection writes, and role-assignment writes. |
| Linux Syslog via AMA | **Completed** | A successful `Syslog` query returned `sshd` events from `gandalf-the-white`, including controlled failed-password activity generated from Sauron. The telemetry was parsed with KQL, investigated, and used with a Microsoft Sentinel SSH brute-force analytics rule. |

Detailed narrative: `docs/part-5-sentinel-integration-security-operations.md`.

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

### Linux SSH investigation

The verified Linux Syslog workflow included:

* Querying `sshd` events from `gandalf-the-white` in the `Syslog` table.
* Isolating controlled failed-password activity generated from Sauron.
* Parsing `SyslogMessage` with KQL to extract investigation fields such as source address, source port, target account, and event type.
* Using the resulting telemetry during investigation and with a Microsoft Sentinel SSH brute-force analytics rule.

### Azure control-plane activity

An `AzureActivity` query successfully returned control-plane records in the Sentinel workspace. The observed operations included SecurityInsights data-connector activity, policy actions, Logic App/connection writes, and role-assignment writes.

Controlled telemetry generation is documented separately from naturally occurring activity so that a successful lab exercise is not mistaken for an uncontrolled attack.

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
| Windows events via AMA       | Completed                   |
| Azure Activity ingestion     | Completed / verified        |
| Linux Syslog ingestion       | Completed / verified        |
| Analytics/detection practice | In progress                 |
| SSH brute-force analytic use | Completed / exercised       |
| Defender XDR integration     | Connected / verified        |
| Status                       | Active training environment |

Sentinel should be treated as an active learning environment rather than a production SIEM.

---

# 11. Microsoft Defender

The current Microsoft Defender evidence supports the following scoped state:

| Component | State | Verified evidence and limitation |
| --- | --- | --- |
| Defender device inventory | **Completed / verified** | The portal inventory contains three devices: `gandalf-the-white` (Linux server), `tgr-bbaggins.tgr.ad.lab` (Windows 11 workstation), and `tgr-adds.tgr.ad.lab` (Windows Server). Inventory presence does not prove every endpoint capability or data table. |
| Microsoft Defender for Identity | **Operational / verified** | `TGR-ADDS` is shown as an onboarded domain controller with server status Running and sensor status Up to date. The portal reports 100% coverage, all domain controllers activated and onboarded, and all sensors healthy and up to date. |
| Defender identity inventory | **Populated / verified** | The inventory contains both on-premises Active Directory identities and cloud/Entra identities. Sensitive identifiers are intentionally omitted here. |
| Defender XDR to Sentinel connector | **Connected / verified** | The Sentinel connector is Connected and shows recent data. For the displayed period it shows 8 incidents and 2 alerts, with data visible for `SecurityIncident` and `SecurityAlert`. |
| Defender for Cloud / CSPM | **In Progress / Needs Verification** | The supplied evidence does not establish the current operational state of these services. |
| Broader endpoint/XDR capabilities | **Needs Verification** | Device inventory presence and connector status do not establish every sensor capability, advanced-hunting table, prevention setting, or response function. |

The XDR connector view does not show verified data for the listed `Device*` tables, so those tables are not represented as active here.

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

A 2026-08-19 observation showed approximately:

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

The later Linux SSH exercise was a distinct controlled simulation: Sauron generated failed-password activity against `gandalf-the-white`, the resulting `sshd` events were verified in `Syslog`, and the telemetry was used for investigation and an SSH brute-force analytics rule.

---

## Windows Endpoint DCR Association

Recreating the `TGR-BBaggins` DCR association restored `SecurityEvent` ingestion. That recovery is verified. The explanation that the prior association or AMA configuration failed to synchronize a modified DCR remains a working hypothesis and is **Needs Verification**.

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

Verified subset:

* Three-device Defender inventory
* Operational Microsoft Defender for Identity sensor on `TGR-ADDS`
* Populated identity inventory
* Connected Defender XDR-to-Sentinel connector with `SecurityIncident` and `SecurityAlert` data

Remaining work includes validating broader endpoint/XDR capabilities and the current Defender for Cloud/CSPM state.

---

## Part 5 — Sentinel Integration

**Status: Completed**

Includes:

* Sentinel workspace and connector configuration
* Windows Security Events via AMA for `TGR-BBaggins` and `TGR-ADDS`
* Curated/custom domain-controller collection and Common endpoint collection
* Verified Azure Activity and Linux Syslog ingestion
* Controlled SSH telemetry generation, KQL investigation, and SSH brute-force analytics-rule use
* Verified Defender XDR-to-Sentinel connection and incident/alert data

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

* [ ] Continue Defender for Cloud configuration where useful
* [ ] Continue building and testing additional Sentinel analytics rules
* [ ] Continue safe simulations for specific learning and detection goals
* [ ] Continue investigating alerts and incidents
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
