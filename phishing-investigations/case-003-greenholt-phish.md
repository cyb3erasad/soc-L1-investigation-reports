# Case Study 003: Phishing Email Investigation with Email Authentication and Attachment Analysis

## 1. Case Overview

| Field | Details |
|---|---|
| Case ID | 003 |
| Investigation Type | Phishing Email Analysis and Attachment Triage |
| Analyst Role | SOC Level 1 Analyst |
| Environment | TryHackMe Greenholt Phish challenge room, simulated activity |
| Tools Used | Email message source review, IP ownership lookup, SPF and DMARC record lookups, `sha256sum`, VirusTotal |
| Sample Reviewed | One suspicious email with an archive attachment |
| Final Classification | True Positive, malicious phishing activity |

## 2. Executive Summary

I investigated a suspicious email with a financial subject line referencing a transfer reference number. The message was sent under the display name Mr. James Jackson, but replies were directed to a different address on a free email service, which is a common sign of reply redirection.

The originating IP address belonged to a web hosting provider, and the sender domain's SPF record authorized only Microsoft's mail servers. The attachment was named to look like a PDF, carried a `.CAB` extension, and was actually a RAR archive. When I hashed the file and searched VirusTotal, 48 of 61 security vendors flagged it as malicious.

The email authentication results, the mismatched reply address, the disguised attachment, and the vendor detections all supported a malicious phishing verdict.

## 3. Evidence Details

**Note:** The malicious sample is not stored in this repository. The file is identified by name and hash only. All domains, IP addresses, and email addresses are defanged.

### Email

- **Subject Line Reference:** Transfer Reference Number 09674321
- **Sender Display Name:** Mr. James Jackson
- **Sender Email Address:** `info@mutawamarine[.]com`
- **Reply To Address:** `info.mutawamarine@mail[.]com`
- **Originating IP Address:** `192[.]119[.]71[.]157`
- **IP Owner:** HostPapa

### Email Authentication (Return-Path Domain)

- **SPF Record:** `v=spf1 include:spf.protection.outlook.com -all`
- **DMARC Record:** `v=DMARC1; p=quarantine; fo=1`

### Attachment

- **File Name:** `SWT_#09674321____PDF__.CAB`
- **SHA-256 Hash:** `2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`
- **File Size:** 400.26 KB
- **Actual File Type:** RAR
- **VirusTotal Result:** 48 of 61 security vendors flagged the file as malicious

## 4. Indicators of Compromise (IOCs)

| Type | Indicator | Context |
|---|---|---|
| Sender email | `info@mutawamarine[.]com` | Address the message was sent from |
| Reply To address | `info.mutawamarine@mail[.]com` | Differs from the sender address and uses a free email provider |
| Display name | Mr. James Jackson | Name shown to the recipient |
| Originating IP | `192[.]119[.]71[.]157` | Owned by HostPapa, a web hosting provider |
| Subject reference | Transfer Reference Number 09674321 | Financial lure; the same number appears in the attachment name |
| Attachment name | `SWT_#09674321____PDF__.CAB` | Suggests a PDF but has a `.CAB` extension |
| File hash (SHA-256) | `2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f` | For threat intelligence lookups and environment searches |
| Actual file type | RAR | Does not match the name or extension |
| Detection ratio | 48 of 61 vendors | Strong consensus that the file is malicious |

## 5. Investigation Steps

### Step 1: Review the Subject and Sender Identity

I reviewed the subject line and the sender details in the message headers.

**Finding:** The subject referenced Transfer Reference Number 09674321. The display name was Mr. James Jackson and the sender address was `info@mutawamarine[.]com`.

**Assessment:** A financial reference in the subject creates urgency and a reason to open the attachment. The display name is a generic personal name attached to a general information mailbox, which is worth noting but not conclusive on its own.

### Step 2: Check the Reply To Address

I compared the sender address with the address that would receive a reply.

**Finding:** Replies would go to `info.mutawamarine@mail[.]com`, not to the sender's own domain.

**Assessment:** A Reply To address that differs from the sender, especially one on a free email service, is a classic indicator of reply redirection. It lets an attacker receive responses even if the sending address is spoofed or disposable.

### Step 3: Trace the Originating IP Address

I analyzed the message source to find the originating IP address, then looked up who owns it.

**Finding:** The originating IP was `192[.]119[.]71[.]157`, owned by HostPapa.

**Assessment:** HostPapa is a web hosting provider. A message presenting itself as coming from a company mailbox but originating from a general hosting provider's IP deserves scrutiny, because attackers commonly send from rented or compromised hosting infrastructure.

### Step 4: Check the SPF Record

I ran an SPF lookup on the Return-Path domain found in the headers.

**Finding:** The record was `v=spf1 include:spf.protection.outlook.com -all`.

**Assessment:** This record authorizes Microsoft's mail servers and ends with `-all`, a hard fail instruction telling receivers to reject mail from any other source. The originating IP belongs to HostPapa, not Microsoft, so this message would not be expected to pass SPF for this domain. The Authentication-Results header would confirm how the receiving server evaluated it.

### Step 5: Check the DMARC Record

I performed a DMARC lookup on the same domain.

**Finding:** The record was `v=DMARC1; p=quarantine; fo=1`.

**Assessment:** A policy of `quarantine` tells receivers to treat mail that fails authentication as suspicious, for example by sending it to spam, but it does not instruct them to reject it outright. The `fo=1` option requests failure reports when any underlying check fails. The record shown includes no reporting address tags. Mail failing authentication can therefore still reach a recipient depending on how their provider handles quarantine.

### Step 6: Examine the Attachment

I reviewed the attachment name and extracted it to the virtual environment for analysis.

**Finding:** The attachment was named `SWT_#09674321____PDF__.CAB`.

**Assessment:** The name contains "PDF" and the same reference number as the subject, which makes it look like a payment document. The extension is `.CAB`, so the name and extension already disagree. This is an attempt to make the file look like a harmless document.

### Step 7: Hash the File and Check VirusTotal

I generated the SHA-256 hash with `sha256sum` and searched it on VirusTotal.

**Finding:** The hash was `2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`. The file was 400.26 KB, VirusTotal identified its actual type as RAR, and 48 of 61 security vendors flagged it as malicious.

**Assessment:** The file claims to be a PDF, has a `.CAB` extension, and is really a RAR archive, so three signals contradict each other. Combined with a very high vendor detection ratio, this indicates a deliberately disguised malicious file. Archives are a common way to deliver malware and to bypass simple file type filters.

### Step 8: Determine the Disposition

I correlated the reply redirection, the hosting provider origin, the SPF and DMARC findings, the disguised attachment, and the VirusTotal results.

**Decision:** Classify the email as malicious phishing and recommend blocking the indicators and removing the message from affected mailboxes.

## 6. Analyst Verdict

**Classification:** True Positive, malicious phishing activity.

**Severity Assessment:** High, because the attachment is widely detected as malicious and the message uses a financial lure with reply redirection. This exercise provides no evidence that anyone opened the file, so actual impact is unconfirmed.

**Justification:** The email combined a mismatched Reply To address, an originating IP that does not match the sender's authorized mail servers, a disguised attachment whose name, extension, and real type all disagree, and a strong vendor consensus that the file is malicious.

## 7. MITRE ATT&CK Mapping

| Tactic | Technique | Relevance to this case |
|---|---|---|
| Initial Access | T1566.001, Phishing: Spearphishing Attachment | Malicious archive delivered as an email attachment |
| Defense Evasion | T1036, Masquerading | File named to resemble a PDF with a `.CAB` extension while actually being a RAR archive |
| Execution | T1204.002, User Execution: Malicious File | Requires the recipient to open the attachment |

The mapping reflects the indicators available at the L1 triage stage and may be refined by deeper analysis.

## 8. Recommended Follow-up Actions

1. Block the sender address, the Reply To address, and the originating IP at the email gateway.
2. Add the SHA-256 hash to endpoint detection and email security blocklists.
3. Search mail logs for other recipients of the same message or attachment and remove it from their mailboxes.
4. Check whether any recipient opened or extracted the attachment, and review endpoint telemetry for follow-on activity if so.
5. Review the Authentication-Results header to confirm the SPF and DMARC outcome on the receiving side.
6. Consider blocking or inspecting archive attachments whose extension does not match their true file type.
7. Notify users about the campaign and reinforce reporting of suspicious emails.
8. Preserve the evidence and document the outcome.

## 9. Lessons Learned

Email authentication records are a quick way to judge whether a sender is legitimate. Comparing the SPF record against the originating IP showed that the sending server did not match the domain's authorized mail infrastructure, and the DMARC policy explained how a receiving system might treat the message.

The file name and extension cannot be trusted. The attachment looked like a PDF, had a `.CAB` extension, and was actually a RAR archive, so checking the real file type and hash was essential. A high VirusTotal detection ratio then gave strong, quick confirmation.

Small details such as a Reply To address that does not match the sender add up. No single indicator proved the email was malicious, but together they left little doubt.

## 10. Training Disclaimer

This case study documents a simulated educational exercise from a TryHackMe challenge room. It does not represent a real-world incident or professional SOC employment experience.