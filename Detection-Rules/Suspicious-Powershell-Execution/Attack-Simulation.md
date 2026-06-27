# PowerShell Attack Simulation

## Overview

This document explains the PowerShell attack simulations performed to validate the Microsoft Sentinel detection rule.

PowerShell is a legitimate Windows administration tool, but attackers commonly abuse it after gaining access to a system.

Attackers use PowerShell because:

- It is already available on Windows systems
- It does not require installing additional tools
- It can execute commands, scripts, and remote content
- It can perform post-compromise activities

The objective of this lab is to simulate common attacker PowerShell behaviors and verify that Microsoft Sentinel can detect them.

The following PowerShell behaviors were simulated:

1. Execution Policy Bypass
2. Encoded PowerShell Command
3. DownloadString and Invoke-Expression Pattern


---

# 1. Execution Policy Bypass Simulation

## Attack Scenario

After gaining access to a Windows machine, an attacker may attempt to execute PowerShell commands while bypassing PowerShell execution restrictions.

PowerShell Execution Policy is a security feature that controls script execution.

Attackers may abuse the bypass option to execute commands without following the configured policy.


## Command Used

powershell.exe -ExecutionPolicy Bypass -Command "whoami"

Explanation

The command contains three parts:

powershell.exe

Starts the PowerShell process.

Attackers commonly use PowerShell because it is a trusted Windows binary and activity may blend with normal administrative operations.

-ExecutionPolicy Bypass

Temporarily bypasses PowerShell execution restrictions.

In a real attack scenario, attackers may use this to execute PowerShell scripts or commands that would otherwise be blocked.

Note:

ExecutionPolicy Bypass alone does not always mean malicious activity.

Administrators may also use it for:

Automation

Testing

Troubleshooting


Detection depends on the overall behavior and context.

whoami

The whoami command displays the current logged-in user.

Attackers use this after compromise to perform user discovery.

They want to identify:

Current user

Domain

Privilege level

Available access


MITRE ATT&CK:

T1059.001 - PowerShell

T1033 - System Owner/User Discovery


---

Simulation Performed

Step 1: Execute Command

Opened the Windows VirtualBox machine.

Started PowerShell.

Executed:

powershell.exe -ExecutionPolicy Bypass -Command "whoami"

<img width="730" height="91" alt="image" src="https://github.com/user-attachments/assets/a89a4fb4-8d13-4e4f-b7d4-b84b52af941a" />


The command executed successfully and returned the current user information.

Step 2: Generate Security Logs

After execution, Windows generated a process creation event.

Event generated:

Event ID: 4688

Event Name:

Process Creation

The event contained:

PowerShell process

Command line

User information

Timestamp


Step 3: Verify Logs

The generated event was verified in Log Analytics Workspace.

The SecurityEvent table showed the PowerShell execution activity.

<img width="1867" height="831" alt="image" src="https://github.com/user-attachments/assets/f264a958-4ef8-4794-b78a-03598ea9b07f" />


Step 4: Sentinel Detection

Microsoft Sentinel Analytics Rule monitored SecurityEvent logs.

The rule identified suspicious PowerShell execution based on:

PowerShell process

Suspicious parameters

Command line indicators

AnalyticalRule For Suspicious PowershellCommand is created which would monitor logs and generates alerts and incidents 
<img width="1878" height="844" alt="image" src="https://github.com/user-attachments/assets/a345d744-8015-48c0-9689-7786215e2915" />



Result:

Alert generated

<img width="1890" height="870" alt="image" src="https://github.com/user-attachments/assets/022f4e17-58d6-4c18-bc71-17359ca2866e" />



Incident created

<img width="1919" height="815" alt="image" src="https://github.com/user-attachments/assets/10da447c-89bb-4f19-8029-d8fd40889fbb" />

<img width="1913" height="845" alt="image" src="https://github.com/user-attachments/assets/d4c753a9-eadd-4bb2-a107-d1a7ebae50f0" />




---

2. Encoded PowerShell Command Simulation

Attack Scenario

Attackers often encode PowerShell commands to hide the actual command being executed.

This technique is commonly used to make analysis harder and bypass simple detections.

Command Used

powershell.exe -EncodedCommand dwBoAG8AYQBtAGkA


Explanation

PowerShell supports encoded commands using:

-EncodedCommand

The encoded value is Base64 format.

The above encoded command represents:

Get-Process

When executed:

PowerShell decodes the value internally and runs the command.

Why Attackers Use Encoded Commands

Attackers use encoding to:

Hide readable commands

Obfuscate scripts

Avoid simple keyword detection

Make investigation difficult


MITRE ATT&CK:

T1027 - Obfuscated Files or Information

T1059.001 - PowerShell


---

Simulation Performed

Step 1: Generate Encoded Command

Created a Base64 encoded PowerShell command.

Step 2: Execute Command

Executed the encoded PowerShell command in the Windows VM.

<img width="852" height="161" alt="image" src="https://github.com/user-attachments/assets/4e31d0c3-30f9-4449-894c-08b0353ea6f4" />


Step 3: Generate Logs

Windows generated:

Event ID: 4688

Process Creation

The event captured:

PowerShell execution

Encoded command parameter

User context


Step 4: Verify Logs

Checked Log Analytics Workspace.

The SecurityEvent table contained the PowerShell execution event.

<img width="1869" height="716" alt="image" src="https://github.com/user-attachments/assets/34c68abe-a28c-4d08-b082-fa403af322f5" />


Step 5: Sentinel Detection

Microsoft Sentinel Analytics Rule detected the encoded PowerShell activity.

Result:


Incident generated

<img width="1869" height="862" alt="image" src="https://github.com/user-attachments/assets/f38e6159-a432-4237-bf00-c86cc17d37cb" />

Alert Created

This alert is created at 11:20PM
<img width="1919" height="667" alt="image" src="https://github.com/user-attachments/assets/48e54a6b-b0a1-41be-a967-d4085fc2985f" />

Alert Details:
<img width="1827" height="828" alt="image" src="https://github.com/user-attachments/assets/d97f793f-f25d-486d-8a4b-7b9064bc2e62" />






---

3. DownloadString and Invoke-Expression Pattern

Attack Scenario

Attackers may use PowerShell to download remote content and execute it directly in memory.

This technique is commonly seen during malware delivery.

Command Pattern

(New-Object Net.WebClient).DownloadString("URL")

Used with:

Invoke-Expression

or:

IEX

Explanation

DownloadString:

Downloads content from a remote location.

Invoke-Expression:

Executes the downloaded PowerShell content.

Attack flow:

Download content

↓

Load into memory

↓

Execute command

Attackers use this because files may not be saved on disk, making detection harder.

MITRE ATT&CK:

T1059.001 - PowerShell

T1105 - Ingress Tool Transfer


---

Detection Validation

All simulations generated Windows process creation telemetry.

Data flow:

Windows VM

↓

Azure Monitor Agent (AMA)

↓

Data Collection Rule (DCR)

↓

Log Analytics Workspace

↓

Microsoft Sentinel

↓

Analytics Rule

↓

Alert

↓

Incident


---

Final Result

Successfully simulated common PowerShell abuse techniques:

1. Execution Policy Bypass


2. Encoded PowerShell Execution


3. Download and Execute Behavior

