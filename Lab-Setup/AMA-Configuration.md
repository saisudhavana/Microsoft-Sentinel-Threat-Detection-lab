
# Azure Monitor Agent (AMA) Installation and Configuration

## Overview

This document explains the installation and configuration of Azure Monitor Agent (AMA) on the Azure Arc enabled Windows Virtual Machine.

In the previous step, the Windows VM running inside VirtualBox was successfully onboarded to Azure using Azure Arc.

At this stage, Azure can recognize and manage the machine, but Azure is still not collecting any logs from the VM.

To collect Windows security logs and send them to Microsoft Sentinel, Azure Monitor Agent (AMA) needs to be installed.

AMA acts as a monitoring agent that collects data from the connected machine and forwards it to Azure services such as Log Analytics Workspace.


## Why Azure Monitor Agent (AMA) is Required?

Azure Arc only connects the external machine to Azure.

It does not automatically collect logs.

To collect:

- Windows Security Events
- System events
- Application events
- Monitoring data

we need an agent installed on the machine.

Azure Monitor Agent provides the communication layer between the Windows VM and Azure monitoring services.


Architecture after AMA installation:

Windows VM (VirtualBox)

↓

Azure Arc Enabled Machine

↓

Azure Monitor Agent (AMA)

↓

Data Collection Rule (DCR)

↓

Log Analytics Workspace

↓

Microsoft Sentinel


---

# AMA Installation Process


## Step 1: Open Azure Arc Machine

Navigate to:

Azure Portal → Azure Arc → Machines


Select the onboarded Windows Virtual Machine.

<img width="940" height="358" alt="image" src="https://github.com/user-attachments/assets/32b9e5b4-c05b-4909-82a5-1cba740cd17d" />


The machine was already visible because Azure Arc onboarding was completed successfully.


---

## Step 2: Open Extensions

Inside the Azure Arc enabled machine:

Navigate to:

Extensions


Extensions allow us to install additional Azure services on the connected machine.

<img width="940" height="326" alt="image" src="https://github.com/user-attachments/assets/f0aee1c7-ce7f-4462-aa87-6d5f88177aa0" />



---

## Step 3: Install Azure Monitor Agent

Selected:

Azure Monitor Agent

<img width="940" height="419" alt="image" src="https://github.com/user-attachments/assets/dad98758-1c6e-460a-a966-037289688150" />


The agent extension was selected for the Windows operating system.


Configuration details were provided and the deployment was started.


---

## Step 4: Deployment

After selecting Azure Monitor Agent:

The deployment process started.


Azure installed the monitoring extension on the Azure Arc enabled Windows VM.


Deployment status:

Succeeded

<img width="940" height="400" alt="image" src="https://github.com/user-attachments/assets/21a4745e-abee-4f0f-9ef6-c7c817b168f7" />


The Azure Monitor Agent installation completed successfully.


---

# Verify AMA Installation


After the deployment was completed:

Open the Azure Arc machine again.


Navigate to:

Azure Arc Machine → Overview


The monitoring status changed and showed that monitoring was installed.

<img width="940" height="347" alt="image" src="https://github.com/user-attachments/assets/68d95d51-3f67-41c0-a210-446301d364a0" />


This confirms that Azure Monitor Agent is successfully installed on the machine.


---

# Result

Azure Monitor Agent is now installed on the Windows VirtualBox machine.

The machine is now ready to send monitoring data to Azure.


The next step is configuring a Data Collection Rule (DCR).

DCR will define:

- Which logs should be collected
- Where the collected data should be sent
- Which Azure resources should receive the data


---

# Next Step

Create and configure a Data Collection Rule (DCR) to collect Windows Security Events and send them to the Log Analytics Workspace used by Microsoft Sentinel.
