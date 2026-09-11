# Windows Defender Application Control (WDAC) Deployment

## 📌 Project Overview

This lab demonstrates the implementation of **Windows Defender Application Control (WDAC)** in a Windows Active Directory environment.

A Code Integrity Policy was created on a Windows golden system, converted from XML to binary format, stored on the domain controller, and deployed through a Group Policy Object (GPO).

This project demonstrates practical skills in **Windows security, PowerShell, Active Directory, Group Policy, Device Guard, and application control**.

---

## 🎯 Objectives

- Create an initial WDAC Code Integrity Policy.
- Scan the entire `C:\` drive.
- Use the `Pca` policy level.
- Include user-mode executables with `-UserPEs`.
- Convert the XML policy into a binary `.bin` policy.
- Store the policy in the CorpDC WDAC share.
- Create a domain-level GPO named `WDAC`.
- Configure Device Guard to deploy the Code Integrity Policy.
- Apply centralized application control through Group Policy.

---

## 🖥️ Lab Environment

| Component | Role |
|---|---|
| **Office2** | Golden system used to create the Code Integrity Policy |
| **CorpDC** | Domain Controller and GPO management |
| **CorpServer** | Hyper-V host used to access CorpDC |
| **CorpNet.local** | Active Directory domain |
| **WDAC** | Network share used to store the policy |
| **Policy File** | `MyCIP.bin` |

### WDAC Policy Location

The WDAC folder on CorpDC is:

```text
C:\WDAC
