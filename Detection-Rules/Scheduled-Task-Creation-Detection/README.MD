# Scheduled Task Creation Detection

## Overview

This project demonstrates the detection of Scheduled Task creation activity using Microsoft Sentinel.

Windows Scheduled Task is a Windows feature that allows programs or scripts to execute automatically based on a specific trigger.

Scheduled Tasks are commonly used for legitimate activities:

- System maintenance
- Backup operations
- Software updates
- Monitoring agents
- Application startup tasks


In this lab, we simulate how attackers abuse Scheduled Tasks for persistence and detect the activity using Microsoft Sentinel.

---

# What is a Scheduled Task?

A Scheduled Task contains two important components:

## 1. Trigger

Trigger decides *when the task should execute*.

Examples:

- User login
- System startup
- Specific time
- Every few minutes
- Daily schedule


Example:


User Login

      ↓

Task Scheduler Starts

      ↓

Configured Action Executes



---

## 2. Action

Action decides *what should execute*.

Examples:

- Application
- PowerShell script
- Batch file
- Executable file


Example:


Trigger:
User Login


      ↓


Action:
powershell.exe script.ps1


---

# Legitimate Scheduled Task Example

Windows and installed software create scheduled tasks for normal operations.

Example:

Azure Monitor Agent (AMA)


Purpose:

- Collect Windows events
- Collect Security logs
- Send heartbeat data
- Send telemetry to Azure


Flow:


Windows VM

      ↓

Azure Monitor Agent Scheduled Task

      ↓

AMA Service Runs

      ↓

Data Collection Rule (DCR)

      ↓

Log Analytics Workspace

      ↓

Microsoft Sentinel



These tasks are expected because they are created by trusted Microsoft components.

---

# Scheduled Task Storage Location

Windows stores scheduled task configurations under:



# C:\Windows\System32\Tasks



Each task contains:

- Task name
- Trigger details
- Action details
- Execution settings


Windows Task Scheduler reads this configuration and executes the defined action.

---

# How Attackers Abuse Scheduled Tasks

Attackers abuse Scheduled Tasks mainly for persistence.


Attack flow:


Attacker Gains Access

          ↓

Creates Scheduled Task

          ↓

Configures Trigger

          ↓

Task Executes Automatically

          ↓

Malicious Code Runs

          ↓

Attacker Maintains Access



---

# Why Attackers Create Scheduled Tasks?


## Persistence


Attackers want access even after:

- System restart
- User logout
- Initial malware removal


Example:



User Logs In

       ↓

Scheduled Task Triggered

       ↓

Malicious PowerShell Executes

       ↓

Attacker Regains Access



---

## Command and Control (C2)


A scheduled task can execute malware that communicates with an attacker-controlled server.


Flow:



Victim Machine

       ↓

Malware Executes

       ↓

Connects To C2 Server

       ↓

Receives Commands



---

## Payload Execution


Attackers can use scheduled tasks to execute:


- PowerShell scripts
- Malware
- Downloaders
- Persistence scripts


Example:


powershell.exe malicious_script.ps1


---

# Lab Simulation

In this lab, we safely simulate Scheduled Task persistence.


Steps performed:



Create PowerShell Script

        ↓

Create Scheduled Task

        ↓

Configure Trigger

        ↓

Configure PowerShell Execution

        ↓

Generate Windows Security Event

        ↓

Detect Using Microsoft Sentinel



---

# Detection

Scheduled Task creation generates:



Event ID: 4698



Event 4698 indicates:



A scheduled task was created



The event contains:

- Task name
- Creator username
- Task configuration
- Executed command


---

# Sentinel Detection Flow



Windows VM

      ↓

Security Event Logs

      ↓

Azure Monitor Agent (AMA)

      ↓

Data Collection Rule (DCR)

      ↓

Log Analytics Workspace

      ↓

SecurityEvent Table

      ↓

Microsoft Sentinel Analytics Rule

      ↓

