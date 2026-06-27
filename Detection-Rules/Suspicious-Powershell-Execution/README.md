
# Suspicious PowerShell Execution Detection

## Overview

This detection rule identifies suspicious PowerShell execution activity on the Windows endpoint.

PowerShell is a legitimate Windows administration tool, but attackers commonly abuse PowerShell for:

- Execution of malicious commands
- Downloading payloads
- Running scripts
- Living-off-the-land attacks
- Bypassing security controls


This detection was created in Microsoft Sentinel using KQL and Windows event telemetry collected through:

- Azure Arc
- Azure Monitor Agent (AMA)
- Data Collection Rule (DCR)
- Log Analytics Workspace


---

# Detection Objective

The goal of this rule is to identify suspicious PowerShell process execution.

The detection monitors:

- PowerShell process creation
- Suspicious command-line usage
- PowerShell execution patterns


---

# Attack Scenario

An attacker or malicious process executes PowerShell on the endpoint.

Example activities:

- Running PowerShell commands
- Executing encoded commands
- Using hidden PowerShell windows
- Downloading scripts


The endpoint generates process execution telemetry.

These events are collected and analyzed by Microsoft Sentinel.


---

# Data Source Used

The detection uses:

Table:

    SecurityEvent


Event:

    Process Creation


Required telemetry:

- Process name
- Command line
- User account
- Computer name


---

# Detection Logic

The analytics rule searches for:

- powershell.exe execution
- Suspicious PowerShell command patterns


The KQL query identifies matching events and creates an alert in Microsoft Sentinel.


---

# MITRE ATT&CK Mapping

Tactic:

Execution


Technique:

Command and Scripting Interpreter


Sub-technique:

PowerShell


MITRE Technique:

T1059.001


---

# Validation

The detection was tested by simulating suspicious PowerShell activity on the Windows VM.

The generated event was collected through AMA and detected by Microsoft Sentinel.


---

# Files in this Folder

## AttackSimulation.md

Contains steps used to simulate suspicious PowerShell execution.


## Detection-Query.kql

Contains the KQL detection logic.


## Rule-Configuration.md

Contains Microsoft Sentinel analytics rule configuration.


## Screenshots

Contains screenshots showing:

- Attack execution
- Log generation
- Sentinel alert
- Rule configuration
