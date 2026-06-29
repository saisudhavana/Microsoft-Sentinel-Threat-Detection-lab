
# New User Creation and Immediate Privilege Escalation Attack Simulation

## Overview

This document explains the simulation performed to detect suspicious user creation followed by immediate administrative privilege assignment.

Attackers who gain access to a Windows endpoint may create a new local account and add that account into privileged groups to maintain access and gain elevated permissions.

This behavior is commonly associated with:

- Persistence
- Privilege Escalation
- Unauthorized account creation


## Detection Flow:

Windows VM

↓

Windows Security Logs

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

Alert and Incident


---

# Attack Scenario

After compromising a Windows machine, an attacker creates a new local user account.

The attacker then adds the created account into the Administrators group.

This gives the attacker elevated privileges on the system.


Attack Flow:


Initial Access

↓

Create New Local User Account

↓

Add User Into Administrators Group

↓

Privilege Escalation

↓

Maintain Access


---

# Why Attackers Create New Users

Attackers create new accounts to maintain access to a compromised system.

Examples:

- Create a hidden backdoor account
- Avoid using the compromised user's account
- Maintain persistence after password changes
- Obtain administrative permissions


#### Example:

An attacker compromises a standard user account.

Instead of using the same account continuously, the attacker creates:


##### attacker1


and assigns administrator privileges.


---

# What is Privilege Escalation?

Privilege escalation means gaining higher permissions than the current user has.


Example:


Normal User

↓

Administrator Account


After privilege escalation, attackers may:

- Install software
- Modify security settings
- Access sensitive data
- Disable security controls


---

# Simulation Environment

The simulation was performed on:

Windows Virtual Machine


The VM generated Windows Security Events.

The events were collected using:

Azure Monitor Agent (AMA)


and stored in:

Log Analytics Workspace


---

# Step 1: Create New Local User

## Command Executed

powershell
net user Hacker1 Password123 /add

<img width="576" height="85" alt="image" src="https://github.com/user-attachments/assets/dd58a2fa-2cb1-429a-bf33-93c82120ea1e" />

---

# Command Explanation


## net user

Windows command used to manage local user accounts.


## attacker1

The username being created.


## Password123

Password assigned to the new account.


## /add

Creates a new local user account.


---

# Expected Result


Output:


The command completed successfully.



This confirms that a new local account was created.


---

# Step 2: Verify Created Users


## Command Executed

powershell
net users

<img width="794" height="206" alt="image" src="https://github.com/user-attachments/assets/8b91ef84-7564-4f73-acdf-0810abce573c" />


---

# Purpose


Displays all local user accounts present on the machine.


The newly created account appears in the user list.


---

# Step 3: Check New User Details


## Command Executed


powershell
net user attacker1

<img width="783" height="536" alt="image" src="https://github.com/user-attachments/assets/4dfbd232-5613-46df-b2ba-7217aeafbeff" />


---

# Purpose


Shows information about the created account.


Before privilege escalation:


Local Group Memberships:


Users



The account has normal user privileges.


---

# Step 4: Add User To Administrators Group


## Command Executed


powershell
net localgroup administrators Hacker1 /add

<img width="737" height="76" alt="image" src="https://github.com/user-attachments/assets/e8879c26-8863-49d5-a6ba-a9e3891a47f3" />




---

# Command Explanation


## net localgroup

Manages local groups.


## administrators

Target privileged group.


## attacker1

User being added.


## /add

Adds the user into the group.


---

# Result


The user is now part of:


Administrators



The account now has elevated privileges.

## Command Executed


powershell
net user attacker1

<img width="765" height="497" alt="image" src="https://github.com/user-attachments/assets/2c7f7120-e890-4d9a-96af-4524e37a194a" />




---

# Before:


Local Group Memberships:


Users



---

# After:


Local Group Memberships:


Users
Administrators



This confirms successful privilege escalation.


---

# Security Logging Requirement


To collect these events:

Audit Policy must be enabled.


Enabled Audit Category:

Account Management

and:

Security Group Management



---

# Windows Event IDs Observed


## Event ID 4720

Meaning:

A user account was created.


Example:

New local account attacker1 created.


---

## Event ID 4732

Meaning:

A member was added to a security-enabled local group.


Example:

attacker1 added to Administrators group.


---

# Log Analytics Validation


Logs were verified in:


Log Analytics Workspace

Table:

SecurityEvent

<img width="1611" height="696" alt="image" src="https://github.com/user-attachments/assets/d2a42e09-11bf-4d13-8cf8-6582b7baa18f" />


---

# Microsoft Sentinel Detection


An Analytics Rule was created to detect:


- New user creation
- User added to privileged group
- Administrators group modification

<img width="1919" height="786" alt="image" src="https://github.com/user-attachments/assets/d6c5b9ef-0e96-49af-9582-336417db1c3f" />



The rule checks:


EventID 4720

OR

EventID 4732


with:


Target group:Administrators

<img width="1331" height="857" alt="image" src="https://github.com/user-attachments/assets/23c0dffa-48ee-4566-8855-f53d9508e78a" />




---

# Alert Generation


Detection Flow:


Suspicious Activity

↓

SecurityEvent Generated

↓

KQL Query Match

↓

Sentinel Alert Created

↓

Incident Created


## Alert Created :
<img width="1582" height="418" alt="image" src="https://github.com/user-attachments/assets/495aa771-3403-4400-b44f-9a8c70b1a7e9" />


## ALert Details:
<img width="1598" height="777" alt="image" src="https://github.com/user-attachments/assets/1fccc442-d055-43ca-bab9-76097e334d9c" />


# Incident Generation
<img width="1876" height="878" alt="image" src="https://github.com/user-attachments/assets/75b0e837-d470-4fb3-9e0a-fd56bbaae5bb" />



---

# Investigation Steps


During investigation, SOC analyst checks:


- Created username
- Creating account
- Time of creation
- Group membership change
- Source machine
- User privileges


---

# Real World Example


A compromised employee laptop is accessed by an attacker.

The attacker creates:


backup_admin


Then adds it to:


Administrators



Now the attacker has persistent administrative access.


---

# MITRE ATT&CK Mapping


## Create Account


T1136
Create Account



## Account Manipulation


T1098
Account Manipulation



---

# Conclusion


This simulation demonstrates how attackers create unauthorized accounts and elevate privileges after gaining access to a Windows endpoint.

Microsoft Sentinel successfully detected the activity using Windows Security Events and generated an alert for investigation.



---
