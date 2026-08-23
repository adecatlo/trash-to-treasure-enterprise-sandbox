# CHANGELOG.md

All notable changes to the cybersecurity enterprise sandbox are documented here.

This changelog records meaningful infrastructure, security, telemetry, and documentation changes.

It is not intended to record every individual command executed during troubleshooting.

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
