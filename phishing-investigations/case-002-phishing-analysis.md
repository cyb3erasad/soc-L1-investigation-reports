# Case Study 002: Phishing Email Analysis with Header Review and Sandbox Detonation

## 1. Case Overview

| Field | Details |
|---|---|
| Case ID | 002 |
| Investigation Type | Phishing Email Analysis and Malware Triage |
| Analyst Role | SOC Level 1 Analyst |
| Environment | TryHackMe phishing analysis lab, simulated activity |
| Tools Used | Thunderbird (message source and header review), ANY.RUN (sandbox analysis) |
| Samples Reviewed | One phishing email impersonating Netflix, a PDF attachment, and an Excel attachment |
| Final Classification | True Positive, malicious phishing activity |

## 2. Executive Summary

I analyzed a phishing email impersonating Netflix, starting with the email headers and then detonating its attachments in the ANY.RUN sandbox.

Header analysis showed the message was addressed to a Yahoo mailbox, and its Return Path domain, `etekno[.]xyz`, had no connection to Netflix. The UPDATE ACCOUNT NOW button pointed to a shortened URL that hid its real destination. The PDF attachment, `Payment-updateid.pdf`, was rated Suspicious Activity by ANY.RUN and contained a link to external infrastructure. The Excel attachment, `CBJ200620039539.xlsx`, launched the Microsoft Equation Editor process (`EQNEDT32.EXE`), which is the pattern used to exploit CVE-2017-11882, and then triggered a network detection tied to a COVID themed download.

Each artifact supported a malicious verdict on its own, and together they show a layered phishing campaign that uses brand impersonation, link obfuscation, and weaponized documents.

## 3. Evidence Details

**Note:** Malicious samples are not stored in this repository. Files are identified by name and hash only and were analyzed in the ANY.RUN sandbox. All URLs, domains, and IP addresses are defanged.

### Email

- **Impersonated Brand:** Netflix
- **Intended Recipient:** `redacted@yahoo.com`
- **Originating Server IP (Received: from):** `209[.]85[.]167[.]226`
- **Return Path:** `postmaster@etekno[.]xyz`
- **Domain of Interest:** `etekno[.]xyz`
- **Call to Action Button Text:** UPDATE ACCOUNT NOW
- **Shortened URL Behind the Button:** `hxxps://t[.]co/yuxfZm8KPg?amp=1`

### PDF Attachment

- **File Name:** `Payment-updateid.pdf`
- **MD5 Hash:** `4a2775eae2ebef41901a3f08d3b857c8`
- **ANY.RUN Classification:** Suspicious Activity
- **URL Found in the Processes Tab:** `hxxps://lihi1[.]com/JqdmJ`
- **IP the Domain Resolves To:** `35[.]244[.]149[.]249`

### Excel Attachment

- **File Name:** `CBJ200620039539.xlsx`
- **Suspicious Process Created:** `EQNEDT32.EXE`
- **Full Path:** `C:\Program Files\Common Files\Microsoft Shared\EQUATION\EQNEDT32.EXE`
- **Vulnerability Exploited:** CVE-2017-11882
- **Follow-up Download:** COVID themed malware
- **Associated Network Threat:** ET INFO Suspicious GET Request with Possible COVID-19 URI M1

## 4. Indicators of Compromise (IOCs)

| Type | Indicator | Context |
|---|---|---|
| Brand impersonation | Netflix | Email designed to look like it came from a trusted streaming service |
| Return Path | `postmaster@etekno[.]xyz` | Domain unrelated to the brand being impersonated |
| Domain | `etekno[.]xyz` | Domain of interest from the Return Path field |
| Originating IP | `209[.]85[.]167[.]226` | Server that delivered the message, taken from the Received: from header |
| Shortened URL | `hxxps://t[.]co/yuxfZm8KPg?amp=1` | Hides the real destination behind the UPDATE ACCOUNT NOW button |
| PDF file name | `Payment-updateid.pdf` | Payment themed lure matching the Netflix impersonation |
| PDF hash (MD5) | `4a2775eae2ebef41901a3f08d3b857c8` | For threat intelligence lookups and environment searches |
| URL in PDF | `hxxps://lihi1[.]com/JqdmJ` | Observed in the sandbox Processes tab |
| Resolved IP | `35[.]244[.]149[.]249` | IP the PDF's domain resolves to |
| Excel file name | `CBJ200620039539.xlsx` | Weaponized spreadsheet |
| Process | `EQNEDT32.EXE` | Legitimate Microsoft Equation Editor component abused by the Excel file; detect the parent and child relationship rather than blocking the file itself |
| Binary path | `C:\Program Files\Common Files\Microsoft Shared\EQUATION\EQNEDT32.EXE` | Location of the abused component |
| CVE | CVE-2017-11882 | Equation Editor vulnerability targeted by the file |
| Network detection | ET INFO Suspicious GET Request with Possible COVID-19 URI M1 | Triggered by the follow-up download of COVID themed malware |

## 5. Investigation Steps

### Step 1: Review the Email Headers

I opened the message source in Thunderbird and reviewed the sender details, the intended recipient, and the Received headers.

**Finding:** The email impersonated Netflix and was addressed to `redacted@yahoo.com`. The originating server IP was `209[.]85[.]167[.]226`.

**Assessment:** A message claiming to come from Netflix should be traced to Netflix's own mail infrastructure. This IP appears to belong to a range associated with Google mail servers, so it should be verified with an IP lookup. If confirmed, it would indicate the message was relayed through a public mail platform and not sent by Netflix.

### Step 2: Examine the Return Path

I reviewed the Return Path field in the message source and compared its domain to the brand shown in the message.

**Finding:** The Return Path was `postmaster@etekno[.]xyz`.

**Assessment:** A Return Path domain unrelated to Netflix is a domain of interest. It suggests the real sending infrastructure differs from what the recipient sees.

### Step 3: Inspect the Link Behind the Button

I inspected the UPDATE ACCOUNT NOW button to see where it pointed instead of trusting the visible text.

**Finding:** The button used a shortened URL: `hxxps://t[.]co/yuxfZm8KPg?amp=1`.

**Assessment:** URL shorteners hide the real destination, so the recipient cannot judge the link by sight. Combined with an urgent account update message, this is a strong phishing indicator.

### Step 4: Analyze the PDF Attachment in ANY.RUN

I submitted the PDF to the ANY.RUN sandbox and reviewed its classification, hash, processes, and network activity.

**Finding:** ANY.RUN classified the sample as Suspicious Activity. The file was named `Payment-updateid.pdf` with MD5 `4a2775eae2ebef41901a3f08d3b857c8`. The Processes tab showed the URL `hxxps://lihi1[.]com/JqdmJ`, and the network connections showed the domain resolving to `35[.]244[.]149[.]249`.

**Assessment:** Suspicious Activity is a less definitive rating than Malicious, so the classification alone would not settle the verdict. In context, though, the payment themed file name matches the Netflix lure, and the PDF connects to a shortened link and external infrastructure. That behavior fits a document used to push the recipient toward a malicious destination.

### Step 5: Analyze the Excel Attachment in ANY.RUN

I submitted the Excel attachment and reviewed its process tree and the details of the processes it created.

**Finding:** After the file was launched, `EQNEDT32.EXE` was created from `C:\Program Files\Common Files\Microsoft Shared\EQUATION\`. The file attempted to exploit CVE-2017-11882 and then downloaded COVID themed malware, which triggered the detection ET INFO Suspicious GET Request with Possible COVID-19 URI M1.

**Assessment:** `EQNEDT32.EXE` is the legitimate Microsoft Equation Editor, so the binary itself is not the malware. The malicious Excel file abused it to run attacker code through a known vulnerability. A spreadsheet that starts Equation Editor and is followed by a suspicious download is a well known exploitation pattern.

### Step 6: Determine the Disposition

I correlated the Netflix impersonation, the mismatched Return Path, the shortened link, the suspicious PDF, and the exploit delivering Excel file.

**Decision:** Classify the email as malicious phishing and recommend blocking the indicators and removing the message from affected mailboxes.

## 6. Analyst Verdict

**Classification:** True Positive, malicious phishing activity.

**Severity Assessment:** High, because the email carries an attachment that exploits a known vulnerability and downloads additional malware.

**Justification:** The email combined brand impersonation, a mismatched Return Path, an obfuscated link, a suspicious PDF, and a weaponized Excel file. Multiple layers produced independent evidence of malicious intent.

## 7. MITRE ATT&CK Mapping

| Tactic | Technique | Relevance to this case |
|---|---|---|
| Initial Access | T1566.001, Phishing: Spearphishing Attachment | PDF and Excel files delivered by email |
| Initial Access | T1566.002, Phishing: Spearphishing Link | Shortened URL behind the UPDATE ACCOUNT NOW button |
| Execution | T1204.002, User Execution: Malicious File | Attachments require the user to open them |
| Execution | T1203, Exploitation for Client Execution | Excel file exploits CVE-2017-11882 through Equation Editor |
| Command and Control | T1105, Ingress Tool Transfer | COVID themed malware downloaded after the exploit |

The mapping reflects the behavior observed in the sandbox and may be refined with further analysis.

## 8. Recommended Follow-up Actions

1. Block the shortened URLs, `etekno[.]xyz`, and the resolved IP at the email gateway, proxy, and firewall.
2. Add the PDF hash and the Excel file to endpoint detection blocklists.
3. Search mail logs for other recipients of the same message and remove it from their mailboxes.
4. Check endpoint telemetry for any user who opened an attachment, looking for `EQNEDT32.EXE` launched by an Office process.
5. Create a detection for Excel or Word spawning `EQNEDT32.EXE`.
6. Verify that Office installations are patched against CVE-2017-11882.
7. Notify users about the campaign and reinforce reporting of suspicious emails.
8. Preserve the evidence and document the outcome.

## 9. Lessons Learned

No single indicator told the whole story. The headers pointed to impersonation, the link hid its destination, and the sandbox revealed what the attachments did. Combining static email analysis with dynamic sandbox analysis gave a more confident verdict than either would alone.

The case also showed that a malicious file does not always drop an obviously malicious binary. Here the exploit abused a legitimate Microsoft component, so detection depends on spotting the unusual parent and child process relationship, not just known bad file names.

Finally, sandbox ratings need context. The PDF was rated Suspicious Activity rather than Malicious, and the verdict still held up because it was supported by the rest of the evidence.

## 10. Training Disclaimer

This case study documents a simulated educational exercise from a TryHackMe lab. It does not represent a real-world incident or professional SOC employment experience.