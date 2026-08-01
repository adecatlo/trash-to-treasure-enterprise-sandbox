# Part 1: Hardware Upgrades & Proxmox VE Foundation

## Overview
Transforming legacy consumer hardware into a dedicated hypervisor host for security monitoring and SIEM deployment.

## Hardware Breakdown
* **Host Chassis:** HP Slimline Desktop
* **CPU:** Intel Core i7-3770 (Upgraded from Intel Pentium G2020)
* **RAM:** 16GB DDR3 (Upgraded from 4GB)
* **Storage:** 500GB SSD (Replaced failing 3.5" HDD)
* **Hypervisor:** Proxmox VE (Type-1)

---

## Operational Pivot
> [!NOTE]
> Originally, this host was intended to run the entire enterprise sandbox, including a dedicated pfSense instance requiring a dual-port 1Gbit NIC. 
> 
> **Resource Assessment:** During testing, compute and RAM constraints meant running pfSense, Wazuh SIEM, and multiple target VMs on a single 16GB node would hit resource bottlenecks. 
> 
> **Decision:** Saved the dual-port NIC for a standalone firewall node. Re-scoped this Proxmox host exclusively as a dedicated **Security Operations & SIEM Node** hosting an Ubuntu Server VM with Wazuh.

---

## Next Steps
- [ ] Spin up target VMs (Windows / Linux)
- [ ] Deploy and register Wazuh agents
- [ ] Verify telemetry pipeline and alert parsing
