# IT Support Case Study: Workstation Performance & Hardware Upgrade

[![OS](https://img.shields.io/badge/OS-Windows%2010-0078D6?style=flat&logo=windows)](https://www.microsoft.com/)
[![Environment](https://img.shields.io/badge/Environment-VirtualBox-183A61?style=flat&logo=virtualbox)](https://www.virtualbox.org/)
[![Tool](https://img.shields.io/badge/Scripting-PowerShell-5391FE?style=flat&logo=powershell)](https://learn.microsoft.com/powershell/)
[![Status](https://img.shields.io/badge/Status-Resolved-success)](#)

## Executive Summary
This case study documents an end-to-end IT Helpdesk Tier 1/Tier 2 incident response. The objective was to diagnose, document, and resolve severe system instability and performance bottlenecks reported by an enterprise user operating within the Microsoft 365 ecosystem.

Through root cause analysis (RCA), hardware inventory auditing, and stress testing, the bottleneck was identified as **physical RAM saturation (4 GB)**, which triggered excessive virtual memory swapping (paging) and secondary CPU spikes. The issue was permanently resolved by optimizing system storage and executing a physical memory upgrade to **8 GB RAM**.

---

## Scenario & Incident Overview
* **Company:** NexaLog Logistics (Fictional Entity)
* **Ticket ID:** `#INC-2026-8942`
* **User:** Ricardo Mendes (Operations & Logistics Analyst)
* **Impact:** High — Frequent system freezes during daily operations.
* **User Symptoms:** System lagging severely when running Microsoft Excel (large datasets), Microsoft Teams (video calls), and Microsoft Edge (multiple tabs).

---

## 🔍 Initial System Audit & Inventory
System metrics collected via Windows Command Line and PowerShell prior to troubleshooting:

| Component | Hardware Specification | Baseline Utilization | Status |
| :--- | :--- | :--- | :--- |
| **Operating System** | Windows 10 Home (64-bit) | Active | OK |
| **CPU** | AMD Ryzen 7 1700X (2 vCPUs) @ 3.40 GHz | Fluctuation: 19% to 100% | Secondary Bottleneck |
| **Installed RAM** | **4.00 GB** | **3.5 GB / 4.0 GB (87% - 90%)** | **CRITICAL BOTTLENECK** |
| **Storage** | 80 GB Virtual SSD (71.9 GB Free) | 4% to 31% Active Time | OK |

---

## Root Cause Analysis (RCA)
1. **Memory Swapping (Paging):** With Microsoft Edge and Microsoft 365 applications loaded, physical memory hit **87%-90% capacity**. 
2. **Disk Thrashing & CPU Spikes:** As physical RAM ran out, Windows forced data into the `pagefile.sys` on disk. The CPU had to manage continuous memory swapping between RAM and disk, causing CPU usage spikes up to **100%** and UI freezes.

---

## Action Plan & Resolution Steps

### Step 1: Temporary Files & Cache Cleanup (Software Maintenance)
Executed a PowerShell maintenance script to clear junk files, user temp folders, and system prefetch logs:

```powershell
# Automated cleanup of temp directories and prefetch cache
Remove-Item -Path "$env:TEMP\*" -Recurse -Force -ErrorAction SilentlyContinue
Remove-Item -Path "C:\Windows\Temp\*" -Recurse -Force -ErrorAction SilentlyContinue
Remove-Item -Path "C:\Windows\Prefetch\*" -Recurse -Force -ErrorAction SilentlyContinue
```
---

## Hardware Memory Upgrade 

1. Gracefully shut down the virtual workstation.

2. Reconfigured host resource allocations in VirtualBox:

   * Base Memory: Upgraded from 4096 MB (4 GB) to 8192 MB (8 GB).

3. Booted the system and verified hardware recognition.

| Metric | Pre-Upgrade (Fault State) | Post-Upgrade (Resolved) | Technical Outcome |
| :--- | :--- | :--- | :--- |
| Total RAM | 4.0 GB | 8.0 GB | +100% Hardware Capacity |
| RAM Utilization | 3.5 GB (87% - 90%) | 4.4 GB (55%) | Operating within safe thresholds |
| Available Headroom | ~0.5 GB | ~3.6 GB | Eliminated forced disk paging |
| CPU Behavior | Spikes up to 100% | Stabilized | No system freezes under load |

---

## Technical Evidence & Documentation

### Evidence 1: High RAM & CPU Stress Test (Pre-fix)
Task Manager demonstrating severe RAM saturation (3.5 GB / 4.0 GB at 87%-90%) and CPU instability caused by running 10 browser tabs and system apps

### Evidence 2: Temporary & Cache Files Audit
Audit of Windows system directories (temp, %temp%, and prefetch) prior to automated cleanup.

### Evidence 3: Post-Upgrade Verification
Task Manager after upgrading to 8.0 GB RAM, confirming stable memory usage at 55% (4.4 GB) under full operational load.
