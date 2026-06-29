
# Microsoft Sentinel Rule Configuration

## Rule Name

New Account Created and Elevated to Local Administrator

---

## Rule Type

Scheduled Query Rule


---

## Data Source

Table:

SecurityEvent


Source:

Windows VM collected through Azure Monitor Agent (AMA)


---

## Detection Logic

The rule detects:

- New local user creation
- User added into Administrators group

<img width="1551" height="514" alt="image" src="https://github.com/user-attachments/assets/13ebe81c-4495-46c0-9454-85db51d08781" />


---

## Event IDs Used

User Creation: 4720

User Added To Group: 4732

---

## KQL Query

SecurityEvent
| where EventID == 4720 or EventID == 4732
| where TimeGenerated > ago(10m)
| where EventID == 4720
   or (EventID == 4732 and TargetUserName == "Administrators")
|extend ist=TimeGenerated+5.5h
| project TimeGenerated,EventID,Computer,SubjectUserName,TargetUserName,MemberName
| order by TimeGenerated desc



---

## Alert Configuration

Severity : Medium

Alert Name : New Account Created and Elevated to Local Administrator


---

## Entity Mapping

Account: SubjectUserName

Host:Computer


---

## Incident Creation

When the query matches:

Security Event

↓

Alert

↓

Incident


---

## MITRE ATT&CK

Create Account:

T1136


Account Manipulation:

T1098
