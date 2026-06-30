# Scheduled Task Persistence - Attack Simulation

## Objective

The objective of this simulation is to create a Windows Scheduled Task that executes a PowerShell script automatically based on a defined trigger.

Attackers commonly abuse Scheduled Tasks as a persistence technique by creating tasks that automatically execute malicious scripts or programs after system events such as user login, system startup, or a scheduled interval.

In this lab, we simulate the creation of a scheduled task and detect the activity using Microsoft Sentinel.

---

# Simulation Steps

## 1. Create PowerShell Script

First, we created a PowerShell script inside the Windows VM.


Location:

C:\Temp

Created file:

Scheduled_Test.ps1

<img width="896" height="192" alt="image" src="https://github.com/user-attachments/assets/f9f2af8c-8335-49a9-a7d6-5a0efc5c02a0" />



The script contains:

Scheduled Task Executes Successfully

Get-Date

The script writes execution information into an output file.

Purpose:

- Verify that the scheduled task is executing successfully
- Capture the execution timestamp
- Provide evidence of task execution


---

# PowerShell Script Logic

The script performs the following action:

- Prints a message indicating scheduled task execution
- Captures the current date and time
- Writes the output into:

C:\Temp\output.txt


The output file uses append mode so every execution adds a new entry instead of overwriting previous results.


Example output:

Scheduled Task Executed Successfully

Date and Time: <execution time>


---

# 2. Verify Script Creation

After creating the script, we verified the file exists in the Temp directory.

Command:

ls


Expected result:

<img width="957" height="231" alt="image" src="https://github.com/user-attachments/assets/cd916585-e8cc-4af4-b762-3d9feee741b1" />


Scheduled_Test.ps1 file is visible inside C:\Temp.


---

# 3. Create Scheduled Task

A scheduled task was created using the Windows Task Scheduler command line utility.

Command used:

schtasks /create /tn Update_Check_Task /tr "powershell.exe -ExecutionPolicy Bypass -File C:\Temp\Scheduled_Test.ps1" /sc minute /mo 1

<img width="1400" height="61" alt="image" src="https://github.com/user-attachments/assets/932eb997-bf80-4201-8077-880333b91bd1" />



Explanation:

schtasks
- Windows command-line utility used to create and manage scheduled tasks.


/create
- Creates a new scheduled task.


/tn Update_Check_Task
- Defines the task name.


/tr
- Specifies the action that the task executes.

In this case:

PowerShell executes Scheduled_Test.ps1


/sc minute
- Defines the schedule type.

The task runs based on minutes.


/mo 1
- Specifies the interval.

The task executes every 1 minute.


---

# Why ExecutionPolicy Bypass Was Used

PowerShell execution policies can restrict script execution.

ExecutionPolicy Bypass allows the script to execute without being blocked by PowerShell policy settings.

In real attacks, adversaries may abuse this option to execute malicious PowerShell scripts.


---

# 4. Verify Scheduled Task Creation

The created task was verified using:

schtasks /query /tn Update_Check_Task

<img width="938" height="146" alt="image" src="https://github.com/user-attachments/assets/4fd4df9b-fde4-4ca8-9986-185bf3914a9f" />




This confirms:

- Task exists
- Task status
- Trigger configuration
- Execution details


---

# 5. Validate Task Execution

After the task was created, Windows automatically executed the PowerShell script every minute.

The script created:

output.txt

<img width="848" height="261" alt="image" src="https://github.com/user-attachments/assets/2dbde343-aab0-4306-8fe8-cf58092ac9cc" />



inside:

C:\Temp


The file contains multiple entries because the task runs repeatedly.
<img width="630" height="397" alt="image" src="https://github.com/user-attachments/assets/b8c84665-b645-4eb0-b2cf-ff6642cc4b76" />


Example:

Scheduled Task Executed Successfully

Time: 10:30

Scheduled Task Executed Successfully

Time: 10:31


This confirms the scheduled task is working.


---

# 6. Enable Audit Policy for Detection

Initially, the scheduled task creation logs were not visible in Microsoft Sentinel.

To collect Scheduled Task creation events, audit logging was enabled.

Configuration changed:

Local Security Policy

Security Settings

Advanced Audit Policy Configuration

Object Access

Audit Other Object Access Events


Enabled:

<img width="940" height="495" alt="image" src="https://github.com/user-attachments/assets/0332ee79-91da-4b8c-bbc8-4eb587c4d07c" />



After enabling the policy, the system generated Scheduled Task creation events.
Command used:

gpupdate /force

<img width="722" height="120" alt="image" src="https://github.com/user-attachments/assets/2d5b1f59-57a7-42a9-9d67-eee34024be3c" />



This applies the updated audit policy immediately.


---

# 7. Event Generated

After creating the scheduled task, Windows generated:

Event ID:

4698

<img width="1675" height="707" alt="image" src="https://github.com/user-attachments/assets/b2aa436d-1c26-4c44-99e3-e9f58adf5bfb" />



Meaning:

A scheduled task was created.


Important fields:

Subject User

Task Name

Task Content

Computer Name

Time Generated


These logs were collected by Azure Monitor Agent and sent to Log Analytics Workspace.

<img width="1919" height="781" alt="image" src="https://github.com/user-attachments/assets/64d55f73-de8e-4d94-b401-e2fae7d88465" />


---

# 8. Analytic Rule Configured

<img width="1910" height="793" alt="image" src="https://github.com/user-attachments/assets/dc8a81b2-b876-4a9f-b7cd-68c5d61d47f4" />

---

# 9. Microsft Sentinel Analytics Rule runs KQL query

<img width="1873" height="786" alt="image" src="https://github.com/user-attachments/assets/654af6c3-629a-4526-a6a3-a03e47e2385f" />


---

# 10. KQL query identifies suspicious Scheduled task activity 

<img width="1635" height="857" alt="image" src="https://github.com/user-attachments/assets/39fc633d-6b57-43c4-b4e4-bd56d3f8c98a" />


---

# 11. Sentinel generates an Alert 

<img width="1144" height="343" alt="image" src="https://github.com/user-attachments/assets/b4fc3701-9eba-48e7-9d16-0cc444cf2b09" />

<img width="1588" height="825" alt="image" src="https://github.com/user-attachments/assets/0b46ab03-123c-4412-9fc9-5126f59db4cd" />



---
# 12. Alert is converted into a Sentinel Incident

<img width="1464" height="851" alt="image" src="https://github.com/user-attachments/assets/22d84ba0-907a-4ba1-905f-8d8e3a19249f" />


---
# 13. Automation Rule runs

<img width="1862" height="852" alt="image" src="https://github.com/user-attachments/assets/d1bcf967-8ed5-47e1-932c-6ab794305b2c" />


---

# 14. Incident Severity/Tags/Task/Assignment Updated

<img width="1525" height="424" alt="image" src="https://github.com/user-attachments/assets/a4be6a91-ea0a-45ab-b7c5-fe88dd8ba639" />


<img width="469" height="771" alt="image" src="https://github.com/user-attachments/assets/854aa8a2-af68-4788-8158-ab61fff9e09c" />



---

# Detection Flow

Windows VM

AMA collects security events

DCR filters and forwards logs

Log Analytics Workspace stores SecurityEvent logs

Microsoft Sentinel Analytics Rule detects Event ID 4698

Alert / Incident is generated


---

# Attack Scenario

Attackers abuse Scheduled Tasks for persistence.

Example:

An attacker gains access to a machine.

They create a scheduled task that executes malicious code whenever:

- User logs in
- System starts
- Specific time interval occurs


Even if the original malware file is removed, the scheduled task can execute again and restore attacker access.

Possible attacker actions after execution:

- Establish Command and Control (C2) communication
- Download additional payloads
- Execute malicious PowerShell commands
- Maintain persistence


---

# Lab Result

Successfully simulated:

Scheduled Task Creation

PowerShell execution through Scheduled Task

Audit logging configuration

Event ID 4698 detection

Microsoft Sentinel alert generation
