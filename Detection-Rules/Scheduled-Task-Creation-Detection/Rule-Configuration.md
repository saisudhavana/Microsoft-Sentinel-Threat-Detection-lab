# Rule Configuration - Suspicious Scheduled Task Creation Using LOLBins

## Rule Name

Suspicious Scheduled Task Creation Using LOLBins

---

## Purpose

This analytic rule is created to detect suspicious Scheduled Task creation activity on Windows endpoints.

Attackers commonly abuse Scheduled Tasks as a persistence technique by creating tasks that automatically execute malicious commands or scripts.

---

## What This Rule Detects

This rule detects:

- Creation of new Windows Scheduled Tasks
- Scheduled Tasks executing suspicious binaries
- Abuse of Windows LOLBins

Examples of monitored binaries:

- powershell.exe
- cmd.exe
- mshta.exe
- rundll32.exe
- regsvr32.exe
- wscript.exe
- cscript.exe

---

# Rule Creation

## Step 1: Open Microsoft Sentinel Analytics
Navigate to:

Microsoft Sentinel

→ Analytics

→ Create

→ Scheduled Query Rule


---

# Rule Details Configuration

Rule Name:

Suspicious Scheduled Task Creation Using LOLBins

<img width="974" height="788" alt="image" src="https://github.com/user-attachments/assets/e5892540-a8fc-4f12-9f29-3a33404101a3" />



Severity:Medium

Status:Enabled

Description:

Detects scheduled tasks created on Windows endpoints that execute suspicious commands or LOLBins.

---

# Automation Configuration

<img width="1873" height="789" alt="image" src="https://github.com/user-attachments/assets/f78710f4-c85a-451c-811e-2af01e1dae16" />



---

# MITRE ATT&CK Mapping

Tactic:

Persistence


Technique:

T1053.005

Scheduled Task/Job: Scheduled Task


---
# Rule Created
<img width="1919" height="662" alt="image" src="https://github.com/user-attachments/assets/8f6d73c6-40fc-4ea6-859a-947066210c7f" />



