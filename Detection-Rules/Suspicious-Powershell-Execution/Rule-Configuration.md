# Microsoft Sentinel Analytics Rule Configuration

## Rule Overview

## Rule Name:

Suspicious PowerShell Execution


## Purpose:

This analytics rule detects suspicious PowerShell execution patterns that are commonly abused by attackers after gaining access to a Windows machine.


## The rule detects:

- PowerShell execution
- Execution Policy Bypass
- Encoded PowerShell commands
- DownloadString usage
- Invoke-Expression (IEX)
- Suspicious PowerShell parameters


## MITRE ATT&CK Mapping:

T1059.001 - Command and Scripting Interpreter: PowerShell
T1027 - Obfuscated Files or Information

T1105 - Ingress Tool Transfer


---

# Rule Creation


## Step 1: Open Analytics Rules

Navigate:

Microsoft Sentinel

↓

Analytics

↓

Create

↓

Scheduled Query Rule


Screenshot:

<img width="1902" height="791" alt="image" src="https://github.com/user-attachments/assets/c65cd291-5ab2-424e-afbf-9cdef7cb88c2" />




---

# Rule Details


Rule Name:Suspicious PowerShell Execution

Severity:Medium

Description:

Detects suspicious PowerShell command execution based on command line arguments and known attacker techniques.


Screenshot:

<img width="1838" height="738" alt="image" src="https://github.com/user-attachments/assets/39907ae8-64e4-43ed-a51a-90ff606596f8" />



---

# Query Configuration


Table Used: SecurityEvent


Reason:

SecurityEvent table contains Windows Security events collected through Azure Monitor Agent (AMA).


The rule monitors:

Event ID 4688

(Process Creation Event)


## KQL Query:

<img width="1912" height="843" alt="image" src="https://github.com/user-attachments/assets/c0271bb7-57bd-486c-97a6-35a779a0b1a1" />


---

# Entity Mapping

## Configured entities:

### Account:AccountName

Host:Computer

Process:NewProcessName

<img width="1816" height="671" alt="image" src="https://github.com/user-attachments/assets/dbe40f52-98ec-4569-8b71-1c091a04341c" />


---

# Alert Settings

## Query Scheduling:

## Run query every:

5 minutes

## Lookup period:

5 minutes

<img width="1257" height="492" alt="image" src="https://github.com/user-attachments/assets/108c27b2-25b0-4f91-827a-62358f783e66" />



---

## Incident Creation

Enabled:

Create incidents from alerts

Reason:

Allows Sentinel to automatically create incidents when suspicious PowerShell execution is detected.

---

