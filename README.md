# Lab Report: Authentication Auditing & Security Log Analysis (Windows)

* **Date of Baseline:** September 8, 2026
* **Target Operating System:** Windows 11 (Host Machine)
* **Target Security Event Identifier:** Event ID 4625 (Audit Failure)
* **Computer Hostname:** `GAL1LEO`

---

## 1. Executive Summary
This laboratory exercise demonstrates the practical discovery and evaluation of credential-based anomalies within a local Windows system. Controlled authentication failures were generated against local subsystem frameworks to monitor logging reactions. 

The resulting Windows event telemetry records were successfully isolated within the Windows Security log framework. Tracking these baseline indicators allows security engineers to build explicit detection profiles for brute-force operations, unauthorized login attempts, and credential-spraying vectors.

---

## 2. Controlled Event Generation
To simulate an active authentication threat pattern without triggering account lockouts or losing administrative session access, consecutive incorrect credential strings were passed to the system. 

The Event Viewer successfully captured a cluster of 4 sequential failed login entries matching this generation phase:
* **Failure Event 1:** 4:26:55 PM
* **Failure Event 2:** 4:26:57 PM
* **Failure Event 3:** 4:26:59 PM
* **Failure Event 4:** 4:27:01 PM *(Selected Frame)*

---

## 3. Windows Security Log Event Analysis
The authentication failure was successfully isolated inside the Windows Event Viewer using specific event filtering arguments. The metadata matches the generation profile exactly:

### Event Viewer Filter Criteria
* **Log Location:** `Windows Logs/Security`
* **Target Event ID:** `4625`

### Decoded Event Log Details
```text
Log Name:      Security
Source:        Microsoft-Windows-Security-Auditing
Logged:        9/8/2026 4:27:01 PM
Event ID:      4625
Task Category: Logon
Level:         Information
Keywords:      Audit Failure
User:          N/A
Computer:      GAL1LEO
Description:
An account failed to log on.

Subject:
    Security ID:        SYSTEM
    Account Name:       GAL1LEO\$
    Account Domain:     WORKGROUP
    Logon ID:           0x3E7
    
Logon Type:             2 (Interactive Logon)
```

![Figure 1: Windows Event Viewer panel isolating Event ID 4625 Audit Failures on GAL1LEO](lmage/windows_auth_failure.png)

---

## 4. Technical Field Mapping & Audit Anatomy
When inspecting Windows Event ID 4625 strings during an incident investigation process, the extracted parameters provide critical triage context:

* **Event ID 4625:** The static universal index mapping an account logon failure sequence.
* **Logon Type `2` (Interactive):** This is a critical forensic indicator. It confirms that the login attempt was made **interactively via the physical keyboard and local logon screen** (or a direct terminal session on the machine), rather than across a network share (Type 3) or a Remote Desktop session (Type 10).
* **Subject Account Name (`GAL1LEO$`):** Shows that the local system account itself managed the initial processing framework for user authentication before rejecting the token.
* **Computer (`GAL1LEO`):** Pinpoints the specific workstation asset where the failed sequence was local to the device architecture.

---

## 5. Security Incident Response Takeaways
1. **Brute Force Identification:** Lone 4625 instances generally indicate user password typos. However, seeing rapid, back-to-back entries spaced exactly 2 seconds apart (as recorded at 4:26:55, 4:26:57, 4:26:59, and 4:27:01) indicates a systematic, rapid entry pattern consistent with automated password guessing or rapid manual attempts.
2. **Logon Type Triage:** Isolating **Logon Type 2** indicates that the physical console or local interface was used. In a production environment, if these failure events appear after hours when no staff are on-site, it points to physical tampering or an unauthorized insider threat attempt.
