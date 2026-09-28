# jpmorgan-incident-audit-review
This is an IT audit case study based on a jp-morgan data breach that happened in 2014.  We reviewed this case study in my information security seminar so i wanted to expand on the case study further.  For this case study I will use ISO 27001/NIST remediation policies and audit testing procedures.
# IT Audit & Control Remediation Case Study: JPMorgan Chase Data Breach

## 1. Executive Summary & Incident Overview
* **Target / Environment:** JPMorgan Chase corporate network infrastructure.
* **Attack Timeline:** Initial intrusion traced back as early as April/June 2014; full discovery and containment completed August/October 2014.
* **Scope of Impact:** Compromise of 90+ internal servers; exposure of personally identifiable information (PII) including names, addresses, phone numbers, email addresses, and internal customer categorization data (mortgage, credit card, private banking) affecting 76 million households and 7 million small businesses.
* **Impact Analysis (CIA Triad):**
  * **Confidentiality (High Breach):** Bulk exfiltration of gigabytes of customer PII and internal system architecture metadata (lists of running applications and internal software programs).
  * **Integrity (High Compromise):** Adversaries acquired root/domain administrator privileges and actively deleted/altered security and system event logs to evade detection.
  * **Availability (Low Direct Impact):** Production banking systems remained online; customer funds and direct transactional accounts were not disrupted.

---

## 2. Root Cause & Audit Deficiencies (Control Gaps)

* **Finding 01 — Inconsistent Multi-Factor Authentication (MFA) Implementation:** 
  * *Audit Finding:* While enterprise-wide 2FA had been procured, a single overlooked network server connected to the VPN gateway failed to receive the two-factor authentication update.
  * *Impact:* Attackers authenticated remotely solely with stolen single-factor credentials.
  * *Standard Ref:* ISO/IEC 27001:2022 Control 5.17 (Authentication Information), 8.5 (Secure Authentication); NIST SP 800-53 IA-2.

* **Finding 02 — Inadequate Network Segmentation & Least Privilege:**
  * *Audit Finding:* Once inside the boundary network via the VPN, the attacker traversed laterally to compromise over 90 internal servers and elevate privileges to domain administrator level.
  * *Impact:* Lack of micro-segmentation and robust role-based access control (RBAC) permitted internal pivot from an external gateway to sensitive internal data stores.
  * *Standard Ref:* ISO/IEC 27001:2022 Control 8.2 (Privileged Access Rights), 8.20 (Network Security); NIST SP 800-53 AC-6.

* **Finding 03 — Log Tampering & Insufficient Centralised Log Forwarding:**
  * *Audit Finding:* Attackers maintained persistence for months and covered operational tracks by deleting local server log files unchecked.
  * *Impact:* Lack of immutable, write-once remote logging prevented early discovery and hampered forensic timeline reconstruction.
  * *Standard Ref:* ISO/IEC 27001:2022 Control 8.15 (Logging); NIST SP 800-53 AU-9 (Protection of Audit Information).

---

## 3. Remediation Policies & Technical Standards

### Standard 1: Enterprise Boundary Access & Universal Multi-Factor Authentication
* **Policy Statement:** All remote network access endpoints, remote desktop services, and Virtual Private Networks (VPNs) terminating on corporate assets must enforce phishing-resistant Multi-Factor Authentication (MFA) without exception.
* **Control Mandate:** 
  1. No server or endpoint gateway shall be brought online into the corporate network directory without mandatory MFA binding verified through automated configuration management.
  2. Multi-factor authentication exclusion lists or exemptions require a documented, time-bound risk waiver signed by the Chief Information Security Officer (CISO), subject to mandatory quarterly re-validation.
* **Technical Enforcement:** Network access control (NAC) integration disabling unauthenticated single-factor session initiations at the firewall layer.

### Standard 2: Centralised Security Logging, Integrity Protection, & Privileged Access Control
* **Policy Statement:** All domain controllers, production servers, and identity stores must forward security telemetry to an isolated, append-only Security Information and Event Management (SIEM) pipeline in real time.
* **Control Mandate:**
  1. Local deletion or truncation of system event logs must trigger an immediate P1 alert to the Security Operations Center (SOC).
  2. Domain and local administrator accounts must follow the Principle of Least Privilege (PoLP); interactive administrator logons must require Privileged Access Management (PAM) jump hosts with automated session recording.
* **Technical Enforcement:** Log stores configured with Write-Once-Read-Many (WORM) storage controls and SIEM ingestion validation heartbeat scripts.

---

## 4. IT Audit Test Procedures (TOD & TOE)

| Control Ref | Control Objective | Test of Design (TOD) | Test of Operating Effectiveness (TOE) |
| :--- | :--- | :--- | :--- |
| **AC-01** (MFA Enforcement) | Ensure 100% of remote ingress points require verified multi-factor authentication. | Inspect remote access architecture diagrams and standard operating procedures (SOPs) to confirm MFA is mandatory for all VPN connections. | Sample 25 inbound VPN sessions across production gateways; verify that every session required and validated a second factor token. |
| **PR-02** (Privilege Management) | Restrict administrator-level rights across servers to authorized personnel only. | Review the Privileged Access Management (PAM) policy to verify automated credential rotation and approval workflows are documented. | Inspect the local Administrators and Domain Admins groups across a random sample of 30 servers; verify no unauthorized accounts or generic shared credentials exist. |
| **AU-03** (Audit Log Protection) | Ensure system audit logs cannot be modified or cleared by unauthorized users or malware. | Review log management configurations to verify real-time log streaming to an off-site, append-only SIEM is mandated. | Attempt manual deletion of a test event log on a sampled server; verify that the deletion event is immediately generated, forwarded to the central SIEM, and raises an automated alert. |

```
