# Part 5: Microsoft Sentinel Integration & Security Operations

## Overview

This phase extends the Azure Arc and Log Analytics foundation documented in Part 3. The focus is moving from basic telemetry availability toward deliberate, workload-specific data collection for Microsoft Sentinel.

The work summarized here was reported during September 2026 and included:

* Migrating Windows security-event collection to Windows Security Events via Azure Monitor Agent (AMA).
* Testing separate per-resource Data Collection Rules (DCRs) for Windows telemetry.
* Progressively tightening Windows event collection to reduce unnecessary ingestion, including a curated domain-controller event set.
* Configuring Azure Activity routing through Azure Policy.
* Completing and exercising Linux Syslog collection through AMA.
* Troubleshooting DCR-association and IPv6-connectivity issues affecting telemetry.

This document distinguishes observed results from working hypotheses. A configuration is not treated as fully verified unless the reported evidence establishes that data reached its intended destination.

---

## Windows Security Events via AMA

The Windows 11 endpoint and Active Directory domain controller were initially migrated from the legacy Security Events connector to **Windows Security Events via AMA**. Each system received its own DCR so collection could be evaluated and tuned by workload.

### Telemetry tuning history

* Broader domain-controller collection produced excessive telemetry for the lab, which motivated progressively tighter filtering and a deliberately curated list of Windows Security Event IDs.
* The Windows 11 endpoint was initially configured for **All Security Events**, then reduced to **Common** events after the broader setting produced more telemetry than the lab needed.

This tuning process demonstrated that successful collection does not by itself make every available event appropriate for continuous ingestion. In a small training environment, telemetry volume, investigative value, and ingestion cost all matter.

### Final architecture

* **TGR-BBaggins:** Windows Security Events via AMA remains configured with the **Common** event collection scope. Its `SecurityEvent` data flows through the endpoint's DCR to the Log Analytics workspace and Microsoft Sentinel.
* **TGR-ADDS:** Windows Security Events via AMA remains configured through `TGR-ADDS-Security-DCR`. The DCR uses custom XPath filters containing a deliberately curated selection of Windows Security Event IDs to control telemetry volume and noise.
* **Domain-controller visibility:** TGR-ADDS continues to provide security visibility through Microsoft Defender for Endpoint and Microsoft Defender for Identity.

### Current-state verification

Direct Azure evidence shows that:

* `TGR-ADDS-Security-DCR` remains present as a Windows DCR with Windows Event Logs as its data source and Azure Monitor Logs as its destination.
* The DCR remains associated with the Azure Arc machine `TGR-ADDS`.
* The Windows Event Logs data source uses **Custom** selection mode with XPath filters for a curated set of Security Event IDs.
* A current Log Analytics query against `SecurityEvent` for TGR-ADDS returned **5,613 events** over the preceding 30 days.
* The most recent event in that query was recorded on September 24, 2026 local time, corresponding to September 25, 2026 UTC.

This establishes that the curated TGR-ADDS collection is the current architecture and that ingestion remains active.

---

## DCR Association Troubleshooting

After the Windows 11 DCR was modified, `SecurityEvent` ingestion from that endpoint stopped even though it had previously been working.

### Observed

Removing and recreating the endpoint's DCR association restored `SecurityEvent` ingestion without reinstalling AMA or changing the Log Analytics workspace.

### Working hypothesis

The existing DCR association or AMA configuration may not have synchronized the modified DCR correctly. Recreating the association may have forced the configuration to be applied again.

The recovery is a user-verified result. The synchronization explanation remains a hypothesis because the underlying mechanism was not independently proven.

---

## Azure Activity Routing

Azure Activity was added through the Microsoft Sentinel Content Hub. Azure Policy was then configured to route subscription Activity Log data to the lab's primary Log Analytics workspace.

The setup included:

* A policy assignment for Activity Log routing.
* A remediation task.
* A system-assigned managed identity with the access required for the policy workflow and diagnostic-setting management.

This work provided hands-on experience with the relationship between Azure Policy, managed identities, RBAC, diagnostic settings, Log Analytics, and Sentinel.

### Verified ingestion

Direct screenshot evidence shows a successful `AzureActivity` query in Microsoft Sentinel against the `tgr-sc200-law` workspace. The returned Azure control-plane records include operations associated with:

* Microsoft SecurityInsights data connectors.
* Azure Policy actions.
* Logic App workflow writes.
* Connection writes.
* Role-assignment writes.

The visible results include completed operations with successful activity status, establishing that Azure Activity records reached the intended workspace and were queryable in Sentinel.

**Status: Completed.** The successful `AzureActivity` query verifies end-to-end ingestion; this status is based on observed query results rather than configuration alone.

---

## Linux Syslog via AMA

Syslog via AMA was configured for the Linux VM **gandalf-the-white** with a dedicated DCR.

During setup, AMA connectivity failed because the VM did not have a usable IPv6 path for outbound Azure connectivity. After the relevant IPv6 prefix and routing configuration was corrected, outbound connectivity returned and AMA began working.

### Verified end-to-end ingestion

Linux authentication events from gandalf-the-white were successfully queried in the Log Analytics and Sentinel `Syslog` table.

Direct screenshot evidence shows a successful `Syslog` query returning records with:

* `Computer` set to `gandalf-the-white`.
* `ProcessName` set to `sshd`.
* Repeated `Failed password` events for the controlled target account.
* The controlled source address and SSH source port extracted from `SyslogMessage`.
* Projected fields for event time, computer, process, event type, source port, target account, and source IP.

The validation query used KQL regular-expression extraction to populate `EventType`, `SourcePort`, `TargetAccount`, and `SourceIP`. The private source IP visible in the validation evidence is intentionally omitted from this public document because the exact internal address is not necessary to establish the result.

The pipeline was subsequently exercised by generating controlled SSH brute-force traffic from the Kali VM **Sauron** against gandalf-the-white. The resulting SSH authentication telemetry was:

* Observed in the `Syslog` table.
* Queried with KQL.
* Parsed from `SyslogMessage` to extract the source IP and `TargetAccount`.
* Used to investigate the controlled SSH activity.
* Used with a Microsoft Sentinel SSH brute-force analytics rule.

The screenshot directly verifies ingestion, query results, and field parsing. The controlled traffic source and analytics-rule use are additional user-verified lab observations.

**Status: Completed.** Together, this evidence establishes successful end-to-end Linux Syslog ingestion and practical use of the collected telemetry in Sentinel.

---

## Current Evidence Summary

| Component | State | Evidence |
| --- | --- | --- |
| TGR-BBaggins Windows Security Events via AMA | **Completed** | The endpoint uses the Common event scope. `SecurityEvent` ingestion was observed after its DCR association was recreated. |
| TGR-ADDS SecurityEvent tuning | **Completed** | Broader collection produced excessive telemetry and motivated progressively tighter collection using custom XPath filters and a curated event set. |
| TGR-ADDS Windows Security Events via AMA | **Completed** | Direct Azure evidence shows `TGR-ADDS-Security-DCR` associated with the TGR-ADDS Azure Arc resource. A current query returned 5,613 `SecurityEvent` records over the preceding 30 days, with the most recent event on September 24, 2026 local time / September 25 UTC. |
| TGR-ADDS security visibility | **Completed** | Security visibility continues through Microsoft Defender for Endpoint and Microsoft Defender for Identity. |
| DCR synchronization root cause | **Needs Verification** | Recreating the association restored ingestion, but the synchronization mechanism remains a working hypothesis. |
| Azure Activity ingestion | **Completed** | Direct screenshot evidence shows a successful `AzureActivity` query in the `tgr-sc200-law` workspace returning SecurityInsights data-connector operations, policy actions, Logic App and connection writes, and role-assignment writes. |
| Linux Syslog ingestion | **Completed** | Direct screenshot evidence shows `sshd` failed-password events from gandalf-the-white in the `Syslog` table and KQL extraction of the source address, source port, target account, and event type. User-verified lab observations also establish investigation and SSH brute-force analytics-rule use after controlled traffic was generated from Sauron. |

---

## Lessons Learned

* More telemetry is not automatically better; collection should match the investigative purpose and lab budget.
* Separate DCRs make collection easier to tune by workload.
* Progressively tighter filters can preserve useful domain-controller telemetry while reducing unnecessary volume and noise.
* Custom XPath filters provide more precise control than broad Windows event-collection scopes when a workload needs a deliberately selected event set.
* Agent health and DCR presence do not by themselves prove that expected records are reaching Log Analytics.
* Recreating an association can be a useful recovery step, but a successful recovery does not prove the underlying root cause.
* Telemetry troubleshooting can cross service boundaries, including endpoint configuration, Azure policy and identity, network routing, and ingestion.
* Controlled SSH activity can validate a Linux telemetry pipeline and provide data for parsing, investigation, and analytics-rule testing.

---

## Next Verification Steps

* Preserve TGR-BBaggins's Common event scope unless a learning objective justifies broader collection.
* Preserve and periodically review the curated TGR-ADDS event set so collection remains aligned with current learning and detection objectives.
* Treat the DCR synchronization explanation as a hypothesis unless additional evidence establishes the underlying cause.
* Continue building and testing Sentinel analytics and investigation workflows against verified data sources.
