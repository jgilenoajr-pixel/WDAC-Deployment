# Windows Defender Application Control (WDAC) Deployment

## 📌 Project Overview

This lab demonstrates the implementation of **Windows Defender Application Control (WDAC)** in a Windows Active Directory environment.

A Code Integrity Policy was created on a Windows golden system, converted from XML to binary format, stored on the domain controller, and deployed through a Group Policy Object (GPO).

This project demonstrates practical skills in **Windows security, PowerShell, Active Directory, Group Policy, and application control**.

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
```

The folder is shared as:

```text
WDAC
```

---

# 1. Create the Initial Code Integrity Policy

The initial Code Integrity Policy was created on **Office2**, the golden system.

### Open PowerShell

Right-click **Start** and select:

**Windows PowerShell (Admin)**

### Create the policy

Run:

```powershell
New-CIPolicy MyCIP.xml -Level Pca -ScanPath C:\ -UserPEs
```

### Command breakdown

| Parameter | Description |
|---|---|
| `New-CIPolicy` | Creates a Code Integrity Policy |
| `MyCIP.xml` | Name of the XML policy file |
| `-Level Pca` | Specifies the policy level |
| `-ScanPath C:\` | Scans the entire C: drive |
| `-UserPEs` | Includes user-mode Portable Executable files |

Wait for the scan to complete successfully.

Expected result:

```text
Scan completed successfully
```

---

# 2. Convert the Policy to Binary

The XML policy was converted to the binary format used for deployment.

Run the following command from PowerShell:

```powershell
ConvertFrom-CIPolicy MyCIP.xml C:\MyCIP.bin
```

The resulting binary policy is:

```text
C:\MyCIP.bin
```

---

# 3. Copy MyCIP.bin to the CorpDC WDAC Share

On **Office2**, open **File Explorer**.

Navigate to:

```text
This PC
└── Local Disk (C:)
```

Locate:

```text
MyCIP.bin
```

Right-click the file and select:

**Copy**

Then navigate to:

```text
Network
└── CorpDC
    └── WDAC
```

Right-click inside the `WDAC` folder and select:

**Paste**

The final policy location on CorpDC is:

```text
C:\WDAC\MyCIP.bin
```

---

# 4. Connect to CorpDC

From the lab environment:

```text
Floor 1
└── Networking Closet
    └── CorpServer
```

On CorpServer, open **Hyper-V Manager**.

Select:

```text
CORPSERVER
```

From the **Virtual Machines** pane, double-click:

```text
CorpDC
```

This opens the CorpDC virtual machine.

---

# 5. Create the WDAC Group Policy Object

On **CorpDC**, open:

**Server Manager → Tools → Group Policy Management**

Maximize the Group Policy Management window.

Navigate to:

```text
Forest: CorpNet.local
└── Domains
    └── CorpNet.local
```

Right-click:

```text
CorpNet.local
```

Select:

**Create a GPO in this domain, and link it here...**

For the GPO name, enter:

```text
WDAC
```

Select **OK**.

---

# 6. Configure the WDAC GPO

In Group Policy Management, expand:

```text
CorpNet.local
```

Right-click:

```text
WDAC
```

Select:

**Edit**

Maximize the Group Policy Management Editor.

Navigate to:

```text
Computer Configuration
└── Policies
    └── Administrative Templates
        └── System
            └── Device Guard
```

In the right pane, locate:

```text
Deploy Windows Defender Application Control
```

Double-click the policy.

---

## Enable the Policy

Select:

```text
Enabled
```

In the **Code Integrity Policy file path** field, enter:

```text
C:\WDAC\MyCIP.bin
```

Select:

**OK**

---

# 7. Final Configuration

The completed deployment should have the following structure:

```text
Office2
│
├── C:\MyCIP.xml
└── C:\MyCIP.bin
          │
          │ Copy through File Explorer
          ▼
CorpDC
│
└── C:\WDAC
    └── MyCIP.bin
          │
          │ Referenced by GPO
          ▼
CorpNet.local
│
└── WDAC
    │
    └── Computer Configuration
        └── Policies
            └── Administrative Templates
                └── System
                    └── Device Guard
                        └── Deploy Windows Defender Application Control
                            ├── Enabled
                            └── C:\WDAC\MyCIP.bin
```

---

# 8. Key PowerShell Commands

### Create the Code Integrity Policy

```powershell
New-CIPolicy MyCIP.xml -Level Pca -ScanPath C:\ -UserPEs
```

### Convert the policy to binary

```powershell
ConvertFrom-CIPolicy MyCIP.xml C:\MyCIP.bin
```

---

# 9. Skills Demonstrated

This lab demonstrates practical experience with:

- Windows Defender Application Control (WDAC)
- Code Integrity Policies
- PowerShell
- Windows Server Administration
- Active Directory
- Group Policy Management
- Device Guard
- Application Control
- Windows Security Hardening
- Centralized Security Policy Deployment

---

# 10. Screenshots

Screenshots document the major stages of the implementation.

### 1. Creating the Code Integrity Policy

![Creating the Code Integrity Policy](screenshots/01-create-cip.png)

### 2. Converting the Policy to Binary

![Converting the Policy](screenshots/02-convert-cip.png)

### 3. MyCIP.bin Stored on CorpDC

![WDAC Share](screenshots/03-wdac-share.png)

### 4. WDAC GPO Created

![WDAC GPO](screenshots/04-create-gpo.png)

### 5. Device Guard Configuration

![Device Guard](screenshots/05-device-guard.png)

### 6. Final WDAC Configuration

![Final WDAC Configuration](screenshots/06-final-configuration.png)

---

# 🛡️ Security Concept

WDAC provides an application control mechanism that can restrict which code is allowed to execute on Windows systems.

In this lab, the organization establishes a trusted baseline from a **golden system** and then uses **Group Policy** to centrally deploy the resulting Code Integrity Policy.

The deployment workflow is:

```text
Golden System
     │
     ▼
Code Integrity Policy
     │
     ▼
Binary Policy
     │
     ▼
Centralized Storage
     │
     ▼
Group Policy
     │
     ▼
Domain Computers
```

This approach demonstrates how organizations can use centralized Windows security controls to improve application execution management.

---

# 📚 Lab Takeaways

### 1. Golden System

A golden system provides the baseline from which the initial Code Integrity Policy is created.

### 2. Code Integrity Policy

The policy defines the code execution rules that WDAC uses to control applications.

### 3. Binary Policy

The XML policy is converted into a binary policy file for deployment.

### 4. Group Policy

Active Directory Group Policy provides centralized management and distribution of the WDAC configuration.

### 5. Device Guard

The Device Guard policy is configured to deploy the Code Integrity Policy to domain computers.

---

## Conclusion

This lab demonstrates an enterprise-oriented approach to Windows application control.

The implementation starts with a golden system, creates a Code Integrity Policy, converts it to binary format, stores it centrally on the domain controller, and configures Group Policy to deploy the policy.

The project demonstrates practical skills in **PowerShell, Windows Server, Active Directory, Group Policy, Device Guard, and Windows security hardening**.
