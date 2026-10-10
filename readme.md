# SOC L1 Investigation Reports

![Focus](https://img.shields.io/badge/Focus-SOC%20Level%201-blue)
![Domain](https://img.shields.io/badge/Domain-Blue%20Team-informational)
![Framework](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red)
![Status](https://img.shields.io/badge/Status-Actively%20Updated-brightgreen)

A collection of hands-on investigation reports written from a SOC Level 1 analyst perspective. Each report documents how I investigate a simulated security incident: what was observed, how it was analyzed, what conclusion was reached, and what action should be taken.

## Purpose

This repository is part of my path toward becoming a Junior SOC Analyst. The goal is to practice the daily work of an L1 analyst, which is triaging alerts, investigating evidence, and documenting findings clearly, and to keep a public record of that practice.

## Investigation Categories

| Folder | Focus | Status |
|--------|-------|--------|
| `phishing-investigations/` | Email-based threats: sender analysis, attachment and hash review, verdict and escalation | Active |
| `windows-log-investigations/` | Windows Event Log analysis: logon activity, account changes, suspicious process and authentication events | In progress |
| `siem-investigations/` | Alert triage and event correlation in a SIEM: searching, filtering, and building a timeline of activity | In progress |

## Case Index

| Case | Title | Type | Verdict | Disposition |
|------|-------|------|---------|-------------|
| [001](phishing-investigations/case-001-phishing-alert.md) | Phishing Alert and Malicious Attachment Investigation | Phishing / Malware Triage | True Positive | Escalated to L2 |

## How I Investigate

Every case follows the same L1 workflow:

1. **Alert / Trigger** - what raised the case and what was reported
2. **Triage** - initial assessment of severity and scope
3. **Evidence Collection** - gathering relevant logs, headers, artifacts, and indicators
4. **Analysis** - examining the evidence and checking indicators against threat intelligence
5. **Verdict** - true positive, false positive, or needs escalation, with reasoning
6. **Recommended Actions** - containment, remediation, and escalation steps
7. **Lessons Learned** - what the case showed and what I would do differently

## Report Structure

Each case study uses the same format:

1. Case Overview (ticket, alert name, severity, status, environment)
2. Executive Summary
3. Evidence Details
4. Indicators of Compromise (IOCs)
5. Investigation Steps, each with a finding and an assessment
6. Analyst Verdict (classification, severity, disposition, justification)
7. MITRE ATT&CK Mapping
8. Recommended Follow-up Actions
9. Lessons Learned
10. Training Disclaimer

## Skills Demonstrated

- Alert triage and escalation decisions
- Phishing email and attachment analysis
- File hash and threat-intelligence review
- Indicator of Compromise (IOC) documentation
- MITRE ATT&CK technique mapping
- Windows log analysis
- SIEM searching and event correlation
- Clear, structured incident documentation

## Repository Structure

```
soc-L1-investigation-reports/
├── phishing-investigations/
│   └── case-001-phishing-alert.md
├── windows-log-investigations/
├── siem-investigations/
└── README.md
```

## Related Projects

- **Nmap Recon Scanner** - Python CLI tool for automated scanning and risky-port flagging
- **SOC Log Parser & Threat Intel Tool** - Python tool that detects SSH brute-force attempts and checks attacker IPs against AbuseIPDB

## Environment and Disclaimer

All cases are based on simulated exercises, including activities from the Google Cybersecurity Professional Certificate, and lab environments. They are for learning purposes and do not represent real-world incidents or professional SOC employment experience. No real organizational data, credentials, or personal information is included, and sensitive details such as real addresses are sanitized.

## About

I'm a Computer Science student working toward a Junior SOC Analyst role. This repository is updated as I complete new investigations.