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

## 4. Investigation Steps

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

**Assessment:** The malicious file identification, combined with the reported user interaction, warranted further endpoint investigation.

### Step 4: Determine the Alert Disposition

I correlated the email identity inconsistencies, suspicious message content, password-protected executable, malicious file hash, and reported attachment opening.

**Decision:** Escalate the alert to SOC Level 2 for further investigation.

## 5. Analyst Verdict

**Classification:** True Positive — Malicious phishing activity.

**Initial Severity:** Medium, as assigned in the ticket.

**Disposition:** Escalated to SOC Level 2.

**Justification:** Multiple indicators supported the phishing assessment, including inconsistent sender identities, suspicious email content, a password-protected executable, and a file hash identified as malicious. The reported opening of the attachment raised concern about potential endpoint impact.

## 6. Recommended Follow-up Actions

1. Review endpoint telemetry to determine whether the executable ran and what activity followed.
2. Investigate related process creation, file modifications, persistence indicators, and network connections.
3. Determine whether the affected endpoint requires containment under the organization's incident response procedures.
4. Search for similar emails and the same attachment across other recipients.
5. Assess potential credential exposure or additional malicious activity where relevant.
6. Preserve investigation evidence and document remediation outcomes.

## 7. Skills Demonstrated

- SOC alert triage
- Phishing email analysis
- Sender identity and impersonation indicators
- Suspicious attachment assessment
- File hash and threat-intelligence review
- Alert classification and escalation
- Incident documentation

## 8. Lessons Learned

This investigation demonstrated the importance of correlating email indicators with file reputation and reported user activity. A suspicious attachment requires more than email analysis alone: endpoint telemetry is needed to establish whether the file executed and whether compromise occurred.

## 9. Training Disclaimer

This case study documents a simulated educational exercise. It does not represent a real-world incident or professional SOC employment experience.