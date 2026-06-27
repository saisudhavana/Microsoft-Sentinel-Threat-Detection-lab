
# Windows Security Events Collection using AMA

## Overview

This document explains how Windows Security Events are collected in Microsoft Sentinel using Azure Monitor Agent (AMA).

After configuring Azure Arc, installing AMA, and creating Data Collection Rules, the next step is enabling the Microsoft Sentinel connector to collect Windows Security Events.

Windows Security Events are required for security monitoring because they contain important information about:

- User authentication
- Login attempts
- Account changes
- Privilege usage
- Security-related activities


---

# Why Windows Security Events are Required?

Azure Monitor Agent collects data from the machine, but Microsoft Sentinel needs specific security event data to perform threat detection.

Windows Security Events provide security telemetry from the Windows operating system.

These events help detect attacks such as:

- Brute force attempts
- Suspicious account creation
- Privilege escalation
- Unauthorized access


The data flow:

Windows VM

↓

Azure Arc

↓

Azure Monitor Agent (AMA)

↓

Windows Security Events Connector

↓

Data Collection Rule (DCR)

↓

Log Analytics Workspace

↓

Microsoft Sentinel


---

# Enable Windows Security Events via AMA Connector


## Step 1: Open Microsoft Sentinel

Navigate to:

Azure Portal

↓

Microsoft Sentinel

↓

Select the Log Analytics Workspace


---

## Step 2: Open Data Connectors

Inside Microsoft Sentinel:

Navigate to:

Content Management

↓

Data Connectors

<img width="1919" height="849" alt="image" src="https://github.com/user-attachments/assets/f8863071-3077-47eb-b486-4557aec5f3eb" />



Search for:

Windows Security Events via AMA


Select the connector.


---

# Step 3: Install Connector

Open:

Windows Security Events via AMA


Select:

Install

<img width="940" height="430" alt="image" src="https://github.com/user-attachments/assets/14496d0c-470a-4c5d-a850-939517c9506c" />



After installation completes, the connector becomes available.


The connector allows Microsoft Sentinel to collect Windows Security Events using Azure Monitor Agent.


---

# Why Use Windows Security Events via AMA Connector?

The connector simplifies the process of collecting Windows security logs.

It automatically helps configure:

- Data collection settings
- Required security event collection
- Connection between Sentinel and AMA


Without the connector, Sentinel will not automatically know which Windows security events should be collected.


---

# Step 4: Configure Windows Security Events Connector

Open:

Windows Security Events via AMA Connector


Navigate to:

Configuration

<img width="940" height="440" alt="image" src="https://github.com/user-attachments/assets/53f5a1f1-0c04-401b-bd4c-d0c9acb78f8f" />



Here we create a Data Collection Rule specifically for Windows Security Events.


---

# Step 5: Create Security Events DCR


Select:

Create Data Collection Rule


Provide DCR name:

Windows-Security-Events-DCR


The DCR is created directly from the Sentinel connector page.


---

# Why Create Another DCR?

A Data Collection Rule defines what data should be collected.

In this lab, two DCRs exist for different purposes.


## DCR 1: Manually Created DCR

Created from:

Azure Portal → Data Collection Rules


Purpose:

- Configure custom Windows Event Logs
- Collect Security logs
- Collect Sysmon logs


This DCR provides more control over event collection.


## DCR 2: Sentinel Connector DCR

Created from:

Microsoft Sentinel → Data Connector → Windows Security Events via AMA


Purpose:

- Collect Windows Security Events required by Sentinel
- Enable security monitoring


This DCR is created specifically for the Sentinel connector workflow.


---

# Step 6: Select Security Events Collection Mode


Inside the connector configuration:

Select:

Collect


Choose:

All Security Events

<img width="940" height="444" alt="image" src="https://github.com/user-attachments/assets/eaa0cb25-57d6-4f3a-838c-f65e63b7257c" />


This allows Sentinel to receive all Windows Security Event logs from the machine.


---

# Step 7: Create DCR

After selecting:

- DCR name
- Target machine
- Security event collection option


Select:

Create Data Collection Rule


Deployment completes successfully.


---

# Step 8: Verify Logs


Open:

Log Analytics Workspace


Navigate to:

Logs


Search:


    SecurityEvent


Example query:


    SecurityEvent
    | take 10


If logs appear, Windows Security Events are successfully collected.

<img width="940" height="445" alt="image" src="https://github.com/user-attachments/assets/4758a11c-f836-4b03-ac05-2335b03ef92b" />



---

# SecurityEvent Table vs Event Table


## SecurityEvent Table

SecurityEvent contains Windows security auditing events.

Examples:

- User login events
- Failed authentication
- Account creation
- Privilege changes


Used for:

- Threat detection
- Investigation
- Analytics Rules


Example:

    SecurityEvent
    | where EventID == 4625


Detects failed login attempts.


---

## Event Table

Event table contains general Windows event logs.

It can include:

- Application events
- System events
- Other Windows logs


It is not focused only on security auditing.


---

# Result

Windows Security Events are successfully connected to Microsoft Sentinel.

The Windows VM is now sending:

- Security events
- Authentication events
- Account activity logs


These logs can now be used to create Sentinel Analytics Rules.

# Next Step

Create Microsoft Sentinel Analytics Rules to detect suspicious activities:
