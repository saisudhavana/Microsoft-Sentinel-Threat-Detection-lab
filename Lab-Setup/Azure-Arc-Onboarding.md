# Azure Arc Onboarding

## Overview

This document explains the Azure Arc onboarding process performed in this Microsoft Sentinel Threat Detection Lab.

In this lab, a Windows Virtual Machine running outside Azure was connected to Azure using Azure Arc.

The Windows VM was created and running locally inside VirtualBox. Since this machine is not an Azure Virtual Machine, Azure cannot automatically identify, manage, or collect logs from it.

Azure Arc is used to extend Azure management capabilities to machines running outside Azure. After onboarding the machine with Azure Arc, Azure can recognize the VM as an Azure-managed resource.

This allows us to:

- Connect external machines to Azure
- Install Azure Monitor Agent (AMA)
- Configure Data Collection Rules (DCR)
- Collect Windows Security Events
- Send logs to Log Analytics Workspace
- Monitor and create detections using Microsoft Sentinel


---

# Lab Architecture

Components used in this project:

- Microsoft Azure Subscription
- Resource Group
- Log Analytics Workspace
- Microsoft Sentinel
- Windows VM (VirtualBox)
- Azure Arc Enabled Machine
- Azure Monitor Agent (AMA)
- Data Collection Rule (DCR)


Architecture Flow:

Windows VM (VirtualBox)
        |
        |
        v
Azure Arc Enabled Machine
        |
        |
        v
Azure Monitor Agent (AMA)
        |
        |
        v
Data Collection Rule (DCR)
        |
        |
        v
Log Analytics Workspace
        |
        |
        v
Microsoft Sentinel


---

# Prerequisites

Before starting Azure Arc onboarding, the following Azure resources were created:

## Azure Resource Group

A Resource Group was created to organize all resources used in this security monitoring lab.


## Log Analytics Workspace

A Log Analytics Workspace was created to store collected logs from the Windows machine.

Microsoft Sentinel was enabled on top of the Log Analytics Workspace to perform security monitoring and threat detection.


---

# Why Azure Arc is Required?

The Windows VM used in this project is not hosted inside Azure.

The VM is running locally using VirtualBox.

Because the machine is outside Azure:

- Azure cannot automatically discover the machine
- Azure cannot install monitoring agents directly
- Security logs cannot be collected from the machine

Azure Arc solves this problem by connecting external machines to Azure.

After Azure Arc onboarding, the Windows VM becomes visible inside Azure as an Arc-enabled machine.

This allows Azure services like Azure Monitor Agent and Microsoft Sentinel to collect and analyze security data.


---

# Azure Arc Onboarding Process


## Step 1: Open Azure Portal

Login to Azure Portal.

Search for:

Azure Arc


Navigate to:

Azure Arc → Machines


At this stage, the VirtualBox Windows VM was not visible because it was not connected to Azure.
![Screenshot](screenshots/Lab-Setup/Screenshots/Azure-Arc.jpg)


---

## Step 2: Add Existing Machine

Selected:

Add/Create machine

Then selected:

Add a machine using Azure Arc


Azure generated an onboarding script.

This script was required to be executed inside the Windows VM.


---

## Step 3: Execute Azure Arc Script on Windows VM

The generated Azure Arc onboarding script was copied.

Inside the VirtualBox Windows VM:

1. Opened PowerShell as Administrator

2. Pasted the Azure Arc onboarding script

3. Executed the script


---

## PowerShell Execution Policy Issue

While running the Azure Arc onboarding script inside the Windows VM, a PowerShell execution policy restriction occurred.

PowerShell execution policy controls which scripts are allowed to run on the system. Since the Azure Arc onboarding script needed to execute, the restriction prevented the script from running successfully.

To allow the script execution temporarily, the following command was used:

    Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope Process


## Why Scope Process Was Used?

The Process scope was selected because:

- It applies only to the current PowerShell session
- It does not permanently modify the system execution policy
- It does not modify machine-wide PowerShell security settings
- Closing PowerShell restores the previous execution policy configuration

This allows the Azure Arc onboarding script to execute without making permanent security changes.


## Step 4: Azure Authentication

After executing the onboarding script:

- Azure authentication popup appeared
- Azure credentials were entered
- Login was completed successfully

The onboarding process continued after successful authentication.


## Step 5: Verify Azure Arc Connection

After successful authentication, the Azure Arc onboarding process completed.

The Windows VirtualBox VM became visible under:

Azure Portal → Azure Arc → Machines


The VM was successfully connected as an Azure Arc-enabled machine.


## Result

Azure can now recognize and manage the Windows VirtualBox machine.

The machine is now ready for:

- Azure Monitor Agent (AMA) installation
- Data Collection Rule (DCR) assignment
- Windows Security Event collection
- Microsoft Sentinel monitoring


## Next Step

The next step is installing Azure Monitor Agent (AMA).

AMA will collect logs from the Azure Arc-enabled machine and send them to the Log Analytics Workspace using Data Collection Rules.
