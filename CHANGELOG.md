# CHANGELOG.md

All notable changes to the cybersecurity enterprise sandbox are documented here.

This changelog records meaningful infrastructure, security, telemetry, and documentation changes.

It is not intended to record every individual command executed during troubleshooting.

---

# 2026-09-24

## Microsoft Sentinel / Telemetry

### Documented

* Recorded the migration of the domain controller and Windows 11 endpoint to Windows Security Events via AMA with separate DCRs.
* Recorded the cost-conscious tuning of domain-controller and endpoint event collection.
* Recorded that recreating the Windows endpoint's DCR association restored `SecurityEvent` ingestion without reinstalling AMA or changing the Log Analytics workspace.
* Preserved the DCR synchronization explanation as a working hypothesis rather than a verified root cause.
* Recorded the Azure Activity policy, remediation, managed-identity, and workspace-routing configuration as **Needs Verification** pending successful data-arrival evidence.
* Recorded Linux Syslog via AMA as **Needs Verification** pending a successful `Syslog` query.
* Recorded that correcting the relevant IPv6 prefix and routing configuration restored Linux outbound connectivity and AMA operation.

## Documentation

### Added

* Added `docs/part-5-sentinel-integration-security-operations.md` as the durable technical record for the Sentinel data-collection and troubleshooting work summarized in the September LinkedIn updates.
* Linked Part 5 from the public README roadmap.

## Current-State Reconciliation and Subsequent Verification

### Telemetry Verified

* Verified Azure Activity ingestion with a successful `AzureActivity` query in `TGR-SC200-LAW` returning Azure control-plane operations. The Microsoft Sentinel Azure Activity connector was also observed in Connected state with recent data. This supersedes the earlier **Needs Verification** status for current-state purposes while preserving that earlier documentation state above.
* Verified Syslog ingestion from `gandalf-the-white` through AMA with successful `Syslog` queries. Controlled SSH failed-password activity generated from the Kali/Sauron attack system was observed, parsed, investigated, and used during Microsoft Sentinel SSH brute-force analytics-rule testing. This supersedes the earlier **Needs Verification** status for current-state purposes while preserving that earlier documentation state above.
* Verified that `TGR-BBaggins` uses Windows Security Events via AMA with the Common collection scope.
* Verified that `TGR-ADDS` remains associated with `TGR-ADDS-Security-DCR`, which uses curated custom XPath Security Event filters. A current 30-day query returned 5,613 `SecurityEvent` records for `TGR-ADDS`, confirming active ingestion.
* Retained the proposed `TGR-BBaggins` DCR synchronization explanation as a working hypothesis; recreating the association restored ingestion, but the root cause remains **Needs Verification**.

### Azure Arc and Defender Verified

* Reconciled the current Azure Arc inventory: `TGR-ADDS`, `TGR-BBaggins`, and `gandalf-the-white` were all observed in Connected state with the monitoring extension installed. This records the current verified inventory and does not assign a common onboarding date to the three machines.
* Verified that Defender Device Inventory contains `TGR-ADDS`, `TGR-BBaggins`, and `gandalf-the-white`.
* Verified that Microsoft Defender for Identity on `TGR-ADDS` is onboarded, running, healthy, and up to date, with 100% lab coverage shown.
* Verified that the Defender identity inventory contains both on-premises Active Directory and Entra identities.
* Verified that the Microsoft Defender XDR connector for Sentinel is Connected and has received `SecurityIncident` and `SecurityAlert` data. This does not establish every Defender/XDR capability or every `Device*` data type.

### Architecture Reconciled

* Recorded that the original Wazuh VM remains retained on the original Proxmox node but is currently powered off/idle.
* Recorded Kali as Proxmox VM 101, display name `Kali-Attack-Box`, on the original node. Its Sauron / `eye-of-sauron` guest identity serves as the controlled attack system.
* Reconciled the primary Proxmox node as hosting OPNsense, the Windows Server domain controller, the Windows 11 endpoint, and the Linux endpoint, while preserving the distinction between Proxmox display names and guest hostnames.

### Documentation Reconciled

* Completed a full current-state reconciliation of `LAB_STATE.md` on 2026-09-24.
* Recorded Part 5 as Completed/documented. Parts 4 and 6 remain In Progress.

### Remaining Needs Verification

* Root cause of the `TGR-BBaggins` DCR synchronization problem.
* Defender for Cloud/CSPM operational state.
* Broader Defender/XDR capabilities beyond the collected evidence.
* Availability and current use of additional XDR `Device*` data types in Sentinel.
* Current licensing/trial state.
* Current ingestion and cost state.

---

# 2026-08-22

## Documentation

### Added

* Established `AGENTS.md` as the operating guidance for AI agents working with the lab repository.
* Established `LAB_STATE.md` as the authoritative current-state record.
* Established `CHANGELOG.md` as the historical record of meaningful lab changes.
* Defined explicit state labels:

  * Completed
  * In Progress
  * Planned
  * Needs Verification
  * Broken
  * Removed

### Documentation Rules

* Current configuration belongs in `LAB_STATE.md`.
* Historical changes belong in `CHANGELOG.md`.
* Project narrative and milestones belong in `README.md`.
* AI agents must not infer completed configuration from planned roadmap items.

---

# 2026-08-19

## Azure / Log Analytics

### Verified

* Log Analytics ingestion was reviewed for the lab.
* Approximately 430 MB of ingestion was observed over the previous seven days.
* Azure ingestion/cost monitoring identified as an ongoing requirement.

### KQL

* Continued investigation of the current `SecurityEvent` schema.
* Confirmed that current schema fields do not always match older SC-200 training examples.
* Continued adapting KQL queries to the actual workspace schema.

---

# 2026-08-16

## Microsoft Sentinel

### Added

* Microsoft Sentinel activated in the lab Log Analytics workspace.
* Sentinel incorporated into the SC-200 training workflow.
* Sample/initial alerts investigated.

### Investigation

* Reviewed Sentinel alert/incident behavior.
* Investigated the distinction between sample alerts and actual lab-generated security activity.
* Reviewed incident correlation settings.

---

# 2026-08-15

## KQL / Event

### Verified

* `Event` telemetry was successfully queried from the Log Analytics workspace.
* Recent Windows security events were observed.
* Continued Event ID analysis.

### Observed Event IDs

Examples observed during investigation included:

* 4624
* 4625
* 4672
* 4688
* 4798
* 4799
* 4907
* 5058

### KQL Practice

* Practiced summarizing security events by Event ID.
* Practiced failed-logon investigation.
* Practiced grouping events by computer, account, and time.
* Investigated the difference between naturally occurring telemetry and simulated attack activity.

---
# 2026-08-14

## Azure Arc / Telemetry

### Completed

* Continued hybrid integration using Azure Arc.
* Continued Windows endpoint telemetry ingestion into Azure.
* Used Log Analytics for security event analysis.

---
# 2026-08-13

## Windows / Active Directory

### Troubleshooting

* Investigated Windows connectivity and ICMP/ping behavior.
* Reviewed whether Group Policy was preventing ping.
* Examined:

  * Domain Group Policy
  * Lab computer baseline
  * Default Domain Policy
  * Windows Defender Firewall
  * File and Printer Sharing rules

### Result

* No definitive evidence was established that the examined Group Policy configuration was responsible for the ping behavior.
* Troubleshooting remains documented as historical context rather than an active configuration change.

---

# Earlier Project Milestones

## Part 4 — Defender / XDR

### Status

In Progress.

### Objectives

* Microsoft Defender XDR
* Defender for Endpoint
* Defender for Cloud
* Endpoint telemetry
* Alert investigation
* Sentinel integration

No component should be marked completed until verified.

---

## Part 3 — Azure Arc & Telemetry Ingestion

### Status

Completed.

### Completed

* Azure Arc integration
* Log Analytics workspace
* Windows security telemetry
* `SecurityEvent` data
* Initial KQL analysis

---

## Part 2 — Infrastructure Expansion, Active Directory & Wazuh

### Status

Completed.

### Completed

* Added a second Proxmox virtualization node using an Amazon Renewed Dell OptiPlex 7060 SFF.
* Added additional compute, memory, and storage capacity to overcome the resource limitations of the original Proxmox node.
* Reused the dual-port 1 Gb NIC from the original system in the new Proxmox node.
* Deployed OPNsense as the network-edge firewall.
* Deployed Windows Server 2025 Evaluation Edition.
* Configured Windows Server 2025 as an Active Directory Domain Controller.
* Configured AD DS, DNS, domain authentication, and Group Policy.
* Deployed Windows 11 Enterprise Evaluation as a domain-joined endpoint.
* Continued using Wazuh agents on Windows Server 2025 and Windows 11 for endpoint security monitoring.
* Connected the Windows infrastructure to Azure using Azure Arc.

### Architectural Outcome

The second Proxmox node became the primary enterprise infrastructure platform while the original Proxmox node remained dedicated to security operations and Wazuh.

---

## Part 1 — Hardware Upgrades & Proxmox VE Foundation

### Status

Completed.

### Completed

* Upgraded an HP Slimline Desktop from an Intel Pentium G2020 to an Intel Core i7-3770.
* Increased memory from 4 GB to 16 GB DDR3.
* Replaced a failing 3.5-inch hard drive with a 500 GB SSD.
* Installed Proxmox VE as the Type-1 hypervisor.
* Initially intended the system to host the entire enterprise security sandbox.
* Evaluated the resource requirements of running OPNsense, Wazuh, and multiple target VMs on a single 16 GB node.
* Identified compute and memory constraints that would limit the planned architecture.

### Architectural Pivot

The original design was re-scoped after resource testing showed that running the firewall, Wazuh SIEM, and multiple target systems on the same 16 GB node would create resource bottlenecks.

The dual-port 1 Gb NIC was retained for use in a future standalone firewall configuration.

The original Proxmox node was subsequently designated as the dedicated **Security Operations / SIEM Node**, hosting an Ubuntu Server VM running Wazuh.

This resource-driven architectural pivot led to the addition of the second Proxmox node documented in Part 2.


---

# Changelog Guidelines

Future entries should use this general structure:

```markdown
# YYYY-MM-DD

## Component

### Added
- What was added.

### Changed
- What changed.

### Verified
- What was actually tested.

### Fixed
- What problem was resolved.

### Known Issues
- Anything still unresolved.
```

Do not use the changelog to claim success merely because a configuration step was attempted.

A failed experiment can be recorded when it provides useful information about the lab.

The objective is to preserve the evolution of the environment, including important troubleshooting discoveries and reversals.
