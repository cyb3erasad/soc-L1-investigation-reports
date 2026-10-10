# Case Study 001: Phishing Alert and Malicious Attachment Investigation

## 1. Case Overview

| Field | Details |
|---|---|
| Alert Ticket | A-2703 |
| Alert Name | SERVER-MAIL Phishing Attempt — Possible Malware Download |
| Initial Severity | Medium |
| Ticket Status | Escalated |
| Analyst Role | SOC Level 1 Analyst |
| Environment | Google Cybersecurity Professional Certificate — simulated activity |
| Investigation Type | Phishing Email and Malware Triage |

## 2. Executive Summary

A phishing alert was generated after an employee downloaded and opened a potentially malicious attachment received through an email. The email contained inconsistencies between the sender's display name, sender address, and the name used in the message body.

The email also contained grammatical errors and a password-protected executable attachment named `bfsvc.exe`. The provided ticket identified the attachment's file hash as known malicious.

Based on these indicators and the employee's interaction with the attachment, I escalated the alert to a SOC Level 2 analyst for further investigation.

## 3. Email Details

- **Sender Display Name:** Def Communications
- **Sender Address:** As recorded in the training ticket; sanitized here
- **Name Used in Email Body:** Clyde West
- **Recipient:** HR department at the simulated organization
- **Subject:** Re: Infrastructure Engineer role
- **Attachment:** `bfsvc.exe`
- **Attachment Type:** Executable file
- **Password Protection:** Yes
- **Reported User Activity:** Attachment downloaded and opened
- **File Hash (SHA-256):** `54e6ea47eb04634d3e87fd7787e2136ccfbcc80ade34f246a12cf93bab527f6b`

## 4. Indicators of Compromise (IOCs)

| Type | Indicator | Context |
|---|---|---|
| File name | `bfsvc.exe` | Executable attachment; the name matches a legitimate Windows system file (see Step 3) |
| File hash (SHA-256) | `54e6ea47eb04634d3e87fd7787e2136ccfbcc80ade34f246a12cf93bab527f6b` | Identified as known malicious in the ticket's threat-intelligence information |
| Attachment type | Password-protected executable | Password protection can prevent automated scanning of the contents |
| Sender display name | Def Communications | Does not match the name used in the email body |
| Name in email body | Clyde West | Inconsistent with the sender display name |
| Subject / pretext | Re: Infrastructure Engineer role | Employment-themed lure sent to the HR department |
| Content anomalies | Grammatical errors; instruction to use a password to open the attachment | Common social engineering traits |

## 5. Investigation Steps

### Step 1: Analyze the Sender Identity

I compared the sender display name, sender email address, and the name used in the body of the email.

**Finding:** The email used inconsistent identities. The sender was displayed as Def Communications, while the body was signed by Clyde West.

**Assessment:** This inconsistency is suspicious and may indicate impersonation or deceptive email content.

### Step 2: Review the Email Content

I reviewed the subject line and message body. The email contained grammatical errors and presented the attachment as a resume and cover letter for an engineering position.

The message also instructed the recipient to use a password to open the attachment.

**Assessment:** The unusual writing, password-protected executable, and employment-related pretext increased suspicion.

### Step 3: Investigate the Attachment

The attachment was named `bfsvc.exe`, and the ticket states that the employee downloaded and opened it.

I reviewed the provided file hash and the ticket's threat-intelligence finding.

**Finding:** The file hash was identified in the training scenario as belonging to a known malicious file.

**Filename observation:** `bfsvc.exe` is also the name of a legitimate Windows system file (the Boot File Servicing Utility), which normally resides in the Windows directory. An attachment delivered by email under that name is not a system file and is consistent with masquerading, where an attacker reuses a trusted filename to appear harmless. Additionally, an attachment presented as a resume or cover letter should be a document, not an executable.

**Assessment:** The malicious file identification, the filename and file-type mismatch, and the reported user interaction warranted further endpoint investigation.

### Step 4: Determine the Alert Disposition

I correlated the email identity inconsistencies, suspicious message content, password-protected executable, malicious file hash, and reported attachment opening.

**Decision:** Escalate the alert to SOC Level 2 for further investigation.

## 6. Analyst Verdict

**Classification:** True Positive — Malicious phishing activity.

**Initial Severity:** Medium, as assigned in the ticket.

**Disposition:** Escalated to SOC Level 2.

**Justification:** Multiple indicators supported the phishing assessment, including inconsistent sender identities, suspicious email content, a password-protected executable, and a file hash identified as malicious. The reported opening of the attachment raised concern about potential endpoint impact.

## 7. MITRE ATT&CK Mapping

| Tactic | Technique | Relevance to this case |
|---|---|---|
| Initial Access | T1566.001 — Phishing: Spearphishing Attachment | Malicious executable delivered as an email attachment with an employment-themed pretext |
| Defense Evasion | T1036 — Masquerading | Executable named `bfsvc.exe`, matching a legitimate Windows filename (possible, pending endpoint review) |
| Execution | T1204.002 — User Execution: Malicious File | Employee reportedly opened the attachment |

The mapping is based on the indicators available at the L1 triage stage and may be refined by endpoint investigation.

## 8. Recommended Follow-up Actions

1. Review endpoint telemetry to determine whether the executable ran and what activity followed.
2. Investigate related process creation, file modifications, persistence indicators, and network connections.
3. Determine whether the affected endpoint requires containment under the organization's incident response procedures.
4. Search for similar emails and the same attachment across other recipients.
5. Assess potential credential exposure or additional malicious activity where relevant.
6. Preserve investigation evidence and document remediation outcomes.

## 9. Skills Demonstrated

- SOC alert triage
- Phishing email analysis
- Sender identity and impersonation indicators
- Suspicious attachment assessment
- File hash and threat-intelligence review
- Indicator of Compromise (IOC) documentation
- MITRE ATT&CK technique mapping
- Alert classification and escalation
- Incident documentation

## 10. Lessons Learned

This investigation demonstrated the importance of correlating email indicators with file reputation and reported user activity. A suspicious attachment requires more than email analysis alone: endpoint telemetry is needed to establish whether the file executed and whether compromise occurred.

It also showed that a filename alone cannot be trusted. A name that matches a legitimate system file, delivered through an unexpected channel, is itself an indicator worth investigating.

## 11. Training Disclaimer

This case study documents a simulated educational exercise. It does not represent a real-world incident or professional SOC employment experience.