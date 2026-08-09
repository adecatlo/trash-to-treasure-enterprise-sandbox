# Part 2: Infrastructure Expansion, Active Directory & Wazuh

## Overview

As the initial Proxmox host reached its resource limitations, I expanded the lab by adding a second virtualization node. This allowed me to introduce additional Windows infrastructure, a dedicated firewall, and 
endpoint security monitoring while continuing to build toward a realistic hybrid Security Operations environment.

---

## 🖥️ Hardware Expansion

The second Proxmox node is an Amazon Renewed Dell OptiPlex 7060 SFF.

### System Specifications

| Component          | Specification              |
| ------------------ | -------------------------- |
| CPU                | Intel Core i7-8700         |
| RAM                | 32 GB                      |
| Primary Storage    | 500 GB NVMe                |
| Additional Storage | 1 TB NVMe via PCIe adapter |
| Network            | 1 Gb dual-port NIC         |
| Hypervisor         | Proxmox VE                 |

The additional 1 TB NVMe SSD provides additional storage for virtual machines and future lab expansion.

The dual-port NIC was salvaged from my previous slimline system and reused in this Proxmox node.

---

## 🏗️ Current Virtual Infrastructure

The second Proxmox node currently hosts the following virtual machines:

### OPNsense

OPNsense is deployed as the network-edge firewall for the lab.

The initial deployment is intentionally straightforward. As the environment grows, the firewall configuration will be expanded to support additional segmentation and security testing.

### Windows Server 2025

A Windows Server 2025 Evaluation Edition virtual machine was deployed and configured as an Active Directory Domain Controller.

Current roles and services include:

* Active Directory Domain Services (AD DS)
* DNS
* Domain authentication
* Group Policy
* Domain management

This server provides the identity infrastructure for the Windows portion of the lab.

### Windows 11 Enterprise

A Windows 11 Enterprise Evaluation virtual machine was deployed as a domain-joined endpoint.

The system is currently being used as a telemetry target and endpoint for security monitoring exercises.

The Windows 11 system was successfully joined to the Active Directory domain hosted by the Windows Server 2025 Domain Controller.

---

## 🛡️ Security Monitoring with Wazuh

Wazuh was deployed to provide endpoint security monitoring and visibility.

Wazuh agents were installed on:

* Windows Server 2025
* Windows 11 Enterprise

The purpose of this deployment is to gain practical experience collecting and analyzing endpoint telemetry.

Areas of interest include:

* Windows security events
* Authentication activity
* Endpoint events
* Security alerts
* Log analysis
* Detection concepts
* Investigation workflows

Additional endpoints and virtual machines will be added as the lab expands.

---

## ☁️ Hybrid Cloud Integration

The next stage of the project is connecting the Windows infrastructure to Microsoft Azure.

The Windows Server 2025 and Windows 11 systems have been onboarded to Azure using Azure Arc.

This provides an opportunity to explore hybrid infrastructure management and begin connecting the on-premises virtual environment with Microsoft cloud security services.

### Planned Azure Security Work

The next phases will focus on:

* Azure Log Analytics Workspace (LAW)
* Endpoint telemetry
* Kusto Query Language (KQL)
* Microsoft Sentinel
* Microsoft Defender for Cloud
* Security event investigation

---

## 🔎 Security Operations Objective

The ultimate goal is to evolve the lab from an infrastructure project into a practical Security Operations environment.

The environment will be used to practice identifying suspicious activity, analyzing security events, developing KQL queries, and investigating potential security incidents.

---

## 📚 SC-200 Preparation

This lab is being developed alongside my preparation for the Microsoft SC-200 Security Operations Analyst certification.

Rather than relying solely on theoretical study, the goal is to use the lab to build practical experience with the technologies and concepts covered by the certification.

The next major milestone is generating meaningful security telemetry and using KQL to investigate that data.

---

## 🚧 Current Status

**Infrastructure:** ✅ Operational

**Proxmox:** ✅ Operational

**OPNsense:** ✅ Deployed

**Windows Server 2025:** ✅ Domain Controller

**Active Directory:** ✅ Operational

**Windows 11:** ✅ Domain Joined

**Wazuh:** ✅ Agents Deployed

**Azure Arc:** ✅ Windows systems connected

**Log Analytics:** 🔄 Next Phase

**KQL:** 🔄 Next Phase

**Microsoft Sentinel:** ⏳ Planned

---

## 🔗 Related Documentation

* [Part 1: Hardware Upgrades & Proxmox VE Foundation](part-1-hardware-prep.md)
* Part 3: Azure Arc & Telemetry Ingestion — Coming Soon
* Part 4: KQL Analysis & Security Operations — Coming Soon
* Part 5: Microsoft Sentinel & Hybrid SecOps — Coming Soon
