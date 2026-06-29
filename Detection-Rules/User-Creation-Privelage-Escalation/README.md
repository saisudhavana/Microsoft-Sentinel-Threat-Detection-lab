
# User Creation and Privilege Escalation Detection Lab


## Overview

This lab demonstrates detection of suspicious Windows user creation followed by administrator privilege assignment.

Attackers who compromise a Windows endpoint may create new local accounts and add those accounts into privileged groups to maintain persistence and gain higher access.

This detection focuses on identifying:

- New local user creation
- Privilege escalation through Administrators group membership
- Suspicious account activity


---

# Attack Scenario

An attacker gains access to a Windows machine.

After gaining access, the attacker creates a new local account.

The attacker then assigns administrator privileges to the newly created account.

## Attack Flow

Initial Access

↓

Create New User Account

↓

Add User Into Administrators Group

↓

Privilege Escalation

↓

Persistence

---

# What is User Account Creation?


A user account is an identity created in Windows which allows a person or application to access the system.

A Windows account contains:

- Username
- Password
- Permissions
- Group memberships


### Example:

When an employee receives a company laptop, IT creates an account for that employee.

The account allows the user to:

- Login into the machine
- Access approved resources
- Run applications based on permissions


---

# Why Are User Accounts Created?


Legitimate user creation happens for:

- New employees
- New administrators
- Application requirements
- Service accounts


Example:

A company creates:

employee01

for a normal user.


An administrator account:

admin01

is created for IT operations.


---

# How Attackers Abuse User Creation


After compromising a machine, attackers may create their own account.

### Example:

hacker1

The attacker creates a new account:


net user hacker1 Password123 /add

---


This gives the attacker another identity on the machine.

Attackers use this technique for:

### Persistence

Maintaining access

Avoiding dependency on the original compromised account


### MITRE ATT&CK:

T1136 - Create Account


---

### What is Privilege Escalation?

Privilege escalation means increasing the permission level of an account.

Example:

Before:

hacker1

Users Group

After:

hacker1

Administrators Group

Administrator accounts have higher privileges.

They can:

Install software

Modify system settings

Change security configurations

Create other users



---

### How Attackers Perform Privilege Escalation

After creating a new account, attackers may add that account into the local Administrators group.

Command:

#### net localgroup administrators hacker1 /add

This changes the account from normal user to administrator.

## MITRE ATT&CK:

T1098 - Account Manipulation


---

Detection Objective

The objective of this detection is to identify:

1. New account creation

Event ID: 4720

2. User added into privileged group

Event ID: 4732

---

Required Windows Auditing

To generate these events, Windows auditing must be enabled.

Audit Policies:

Account Management

and

Security Group Management

These policies allow Windows to record account changes.

---

Data Flow

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

Analytics Rules

↓

Alerts & Incidents


---

### Detection Result

The simulation generated:

Security Event logs

Sentinel Alert

Sentinel Incident



---
