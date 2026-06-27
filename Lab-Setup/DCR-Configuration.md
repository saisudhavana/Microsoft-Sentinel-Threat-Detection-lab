# Data Collection Rule (DCR) Configuration

## Overview

This document explains the creation and configuration of a Data Collection Rule (DCR) in Microsoft Azure.

After completing Azure Arc onboarding and installing Azure Monitor Agent (AMA), the next step is configuring DCR.

The Windows VM is now connected to Azure, and AMA is installed. However, AMA does not know:

- Which logs should be collected
- Which event channels should be monitored
- Where the collected logs should be stored

Data Collection Rule (DCR) provides these instructions to Azure Monitor Agent.


## What is Data Collection Rule (DCR)?

Data Collection Rule is an Azure configuration that defines the data collection process.

DCR controls:

- Data sources
- Log filtering
- Destination
- Target machines


In this project, DCR is responsible for collecting:

- Windows Security Event Logs
- Sysmon Operational Logs


and sending them to:

- Log Analytics Workspace


---

# Why DCR is Required?

Azure Arc connects the Windows VM to Azure.

Azure Monitor Agent collects data from the machine.

But without DCR:

- AMA does not know what logs to collect
- No destination is defined
- Logs will not reach Log Analytics Workspace


DCR acts as a bridge between AMA and Log Analytics Workspace.


Architecture flow:

Windows VM (VirtualBox)

↓

Azure Arc Enabled Machine

↓

Azure Monitor Agent (AMA)

↓

Data Collection Rule (DCR)

↓

Log Analytics Workspace (Storage and Query Layer)

↓

Microsoft Sentinel (Detection and Investigation)


---

# DCR Configuration Process


## Step 1: Open Data Collection Rules

Login to Azure Portal.

Search for:

Data Collection Rules

<img width="940" height="383" alt="image" src="https://github.com/user-attachments/assets/2eb89b38-9a95-4108-a11e-93674311ab07" />


Navigate to:

Azure Monitor → Data Collection Rules


Select:

Create

<img width="940" height="405" alt="image" src="https://github.com/user-attachments/assets/2c66077d-9e72-46dc-aef9-a323c66fca0d" />



---

## Step 2: Create Data Collection Rule

While creating the DCR, provide:

- Subscription
- Resource Group
- Region
- Data Collection Rule name


The rule is created to define the log collection configuration.


---

# Step 3: Add Data Source

During DCR creation:

Navigate to:

Data Sources


Select:

Add Data Source

<img width="940" height="691" alt="image" src="https://github.com/user-attachments/assets/db077b23-c99a-4c84-bcbf-dcf3a22c612e" />


A new configuration page opens.


---

# Step 4: Configure Windows Event Logs

Select:

Data Source Type:

Windows Event Logs


Event Log Selection Mode:

Custom


Custom mode is selected because we need control over which events are collected.


---

# Step 5: Configure Event Logs Using XPath

XPath queries are used to filter and collect required Windows event logs.


The following logs were configured:


## Windows Security Logs

Security logs were added to collect authentication and security-related events.

Configured event channel:

    Security!*[System[(band(Keywords,13510798882111488))]]


These logs help detect:

- Login attempts
- Account changes
- Privilege-related activities


## Sysmon Operational Logs

Sysmon provides detailed endpoint telemetry.

Configured event channel:

    Microsoft-Windows-Sysmon/Operational


Sysmon logs provide information about:

- Process creation
- Network connections
- File activity
- System changes

<img width="940" height="423" alt="image" src="https://github.com/user-attachments/assets/5cdbf8ab-070b-4454-99c3-d04922b645a5" />



---

# Step 6: Configure Destination

After configuring the data source:

Navigate to:

Destination


Select:

Destination Type:

Log Analytics Workspace

<img width="940" height="364" alt="image" src="https://github.com/user-attachments/assets/8247aa45-66d9-4842-818a-148814c64dea" />



---

# Why Log Analytics Workspace is Required?

Log Analytics Workspace is the central location where collected monitoring and security data is stored.

In this project:

- Azure Monitor Agent collects logs from the Windows VM
- DCR decides which logs should be collected
- Log Analytics Workspace stores the collected logs
- Microsoft Sentinel analyzes the logs and creates detections


The collected data includes:

- Windows Security Events
- Sysmon Events
- Heartbeat information


The Log Analytics Workspace acts as the storage and query layer for Microsoft Sentinel.


---

# Select Log Analytics Workspace

While configuring destination:

Select:

Subscription

↓

Resource Group

↓

Log Analytics Workspace


The Log Analytics Workspace created during the Sentinel setup was selected.


After selecting the workspace:

Apply the data source configuration.


---

# Step 7: Create DCR

After configuring:

- Data Source
- XPath Queries
- Destination


Click:

Review + Create


<img width="940" height="877" alt="image" src="https://github.com/user-attachments/assets/d7aeec2a-fe14-4cf5-b06d-61630e3be021" />



The Data Collection Rule deployment starts.


Deployment Status:

Succeeded

<img width="940" height="351" alt="image" src="https://github.com/user-attachments/assets/9c00b32b-4bef-4564-badf-222e8e102e67" />



The DCR was successfully created.
<img width="940" height="397" alt="image" src="https://github.com/user-attachments/assets/87335146-6c89-4b20-b3f9-9b3501a4019a" />



---

# Step 8: Add Azure Arc Machine to DCR

Creating a DCR alone is not enough.

The DCR must be assigned to the machine from which logs should be collected.


Open the created DCR.


Navigate to:

Configuration → Resources


Select:

Add Resource


Select the Azure Arc enabled Windows VM.

<img width="940" height="419" alt="image" src="https://github.com/user-attachments/assets/c28eb152-6ecc-4a6c-a230-6f487f531563" />



Apply the changes.


The Azure Arc machine is now associated with the DCR.

<img width="940" height="360" alt="image" src="https://github.com/user-attachments/assets/56abb3fb-a3b6-4a2d-be49-b6be37642be2" />



---

# Why Azure Arc Machine is Added to DCR?

DCR contains the instructions for collecting logs.

However, it needs a target machine where Azure Monitor Agent is installed.

By assigning the Azure Arc machine:

- AMA receives the DCR configuration
- Selected logs are collected
- Logs are forwarded to Log Analytics Workspace


Without adding the machine:

- AMA is installed
- DCR exists
- But no logs are collected


---

# Step 9: Validate Log Collection

After assigning the machine, verify that logs are reaching Log Analytics Workspace.


Open:

Log Analytics Workspace


Navigate to:

Logs


Test connectivity using:

    Heartbeat


Heartbeat confirms that the machine is connected and sending data.


Example:

    Heartbeat
    | take 20


If successful, heartbeat records from the Windows VM will appear.

<img width="940" height="417" alt="image" src="https://github.com/user-attachments/assets/7ea213a6-1651-426a-9d1e-6d28d3864d59" />



---

# Verify Windows Event Logs

To verify Windows Security logs:


   Event
   |where EventID == 4624

<img width="940" height="389" alt="image" src="https://github.com/user-attachments/assets/2d3e01fb-a3b9-42a7-b413-2b207b38fc98" />



This confirms that Windows event logs are collected through:

Azure Monitor Agent → DCR → Log Analytics Workspace


---

# Result

The Data Collection Rule configuration is completed successfully.


The Windows VirtualBox machine is now:

- Connected through Azure Arc
- Using Azure Monitor Agent
- Assigned to Data Collection Rule
- Sending Security and Sysmon logs
- Storing logs in Log Analytics Workspace
- Ready for Microsoft Sentinel detection rules


---

# Next Step

The next step is creating Windows Security Events via AMA

These rules will detect suspicious activities such as:

- Brute Force Attempts
- Suspicious PowerShell Execution
- User Creation with Privilege Escalation
