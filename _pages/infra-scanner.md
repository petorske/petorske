---
title: "Infrastructure Security Scanner"
permalink: /projects/infra-scanner/
toc: true
toc_label: "Contents"
toc_icon: "shield-alt"
---

A Python-based AWS security scanner that audits cloud infrastructure against CIS benchmarks and security best practices, producing structured findings in JSON and console formats.

**GitHub:** [petorske/infrastructure-security-scanner](https://github.com/petorske/infrastructure-security-scanner)

---

## Overview

Built to automate the tedious work of manually reviewing AWS environments for common misconfigurations, this scanner runs read-only checks across S3, IAM, and network resources, then surfaces actionable findings mapped to compliance controls.

| Attribute | Detail |
|---|---|
| Language | Python 3.9+ |
| Cloud Provider | AWS (S3, IAM, EC2/Network) |
| Output Formats | Console, JSON |
| Compliance | CIS AWS Foundations Benchmark |
| Auth | AWS credentials / IAM roles |

---

## Features

- **S3 Security Assessment** — public access block status, encryption type (SSE-S3 vs SSE-KMS), versioning, and access logging
- **IAM Security Review** — MFA enforcement, access key age, overly permissive policy detection
- **Network Security Analysis** — security group misconfigurations, publicly exposed ports
- **Structured Findings** — each finding carries severity, remediation steps, and CIS control mapping
- **Multiple Report Formats** — human-readable console output and machine-readable JSON export

---

## Architecture

```
infrastructure-security-scanner/
├── scanner/
│   ├── cli.py              # Click-based CLI entry point
│   ├── aws/
│   │   ├── client.py       # Boto3 session management
│   │   └── s3_scanner.py   # S3 security checks
│   ├── models/
│   │   └── finding.py      # Finding dataclass / schema
│   └── reporting/          # JSON, HTML, console renderers
├── templates/              # Jinja2 HTML report templates
└── tests/                  # Unit tests (mocked AWS calls)
```

---

## Usage

```bash
# Full scan with console output
python -m scanner.cli scan --profile default

# Scan specific services
python -m scanner.cli scan --services s3,iam

# Export JSON findings
python -m scanner.cli scan --output-json findings.json

# Generate HTML report
python -m scanner.cli scan --output-html report.html
```

---

## Sample Scan Output

The findings below are from a real scan run against test S3 buckets in a sandbox AWS account. The account ID has been redacted.

### Scan Summary

| Field | Value |
|---|---|
| Account | `[redacted]` |
| Region | `us-east-2` |
| Scan Time | `2026-02-19T20:25:58Z` |
| Total Findings | **14** |
| Critical | 0 |
| High | **2** |
| Medium | **8** |
| Low | **4** |

### Findings

| Severity | Title | Resource | CIS Control |
|---|---|---|---|
| 🔴 HIGH | S3 Bucket Allows Public Access | `test-scanner-public-*` | CIS AWS 2.1.5 |
| 🔴 HIGH | S3 Bucket Allows Public Access | `test-scanner-public-*` | CIS AWS 2.1.5 |
| 🟡 MEDIUM | S3 Bucket Using SSE-S3 Encryption (KMS Recommended) | `test-scanner-public-*` | CIS AWS 2.1.1 |
| 🟡 MEDIUM | S3 Bucket Using SSE-S3 Encryption (KMS Recommended) | `test-scanner-public-*` | CIS AWS 2.1.1 |
| 🟡 MEDIUM | S3 Bucket Using SSE-S3 Encryption (KMS Recommended) | `test-scanner-unencrypted-*` | CIS AWS 2.1.1 |
| 🟡 MEDIUM | S3 Bucket Using SSE-S3 Encryption (KMS Recommended) | `test-scanner-unencrypted-*` | CIS AWS 2.1.1 |
| 🟡 MEDIUM | S3 Bucket Versioning Not Enabled | `test-scanner-public-*` | CIS AWS 2.1.3 |
| 🟡 MEDIUM | S3 Bucket Versioning Not Enabled | `test-scanner-public-*` | CIS AWS 2.1.3 |
| 🟡 MEDIUM | S3 Bucket Versioning Not Enabled | `test-scanner-unencrypted-*` | CIS AWS 2.1.3 |
| 🟡 MEDIUM | S3 Bucket Versioning Not Enabled | `test-scanner-unencrypted-*` | CIS AWS 2.1.3 |
| 🔵 LOW | S3 Bucket Access Logging Not Enabled | `test-scanner-public-*` | CIS AWS 2.1.4 |
| 🔵 LOW | S3 Bucket Access Logging Not Enabled | `test-scanner-public-*` | CIS AWS 2.1.4 |
| 🔵 LOW | S3 Bucket Access Logging Not Enabled | `test-scanner-unencrypted-*` | CIS AWS 2.1.4 |
| 🔵 LOW | S3 Bucket Access Logging Not Enabled | `test-scanner-unencrypted-*` | CIS AWS 2.1.4 |

### Example Finding (JSON)

```json
{
  "title": "S3 Bucket Allows Public Access",
  "description": "Bucket does not have all public access blocks enabled",
  "severity": "HIGH",
  "resource_type": "s3-bucket",
  "region": "us-east-2",
  "recommendation": "Enable all public access block settings unless public access is explicitly required",
  "remediation": "AWS Console: S3 > Bucket > Permissions > Block public access > Edit > Enable all settings",
  "compliance_frameworks": ["CIS AWS 2.1.5"],
  "status": "OPEN",
  "raw_data": {
    "public_access_config": {
      "BlockPublicAcls": false,
      "IgnorePublicAcls": false,
      "BlockPublicPolicy": false,
      "RestrictPublicBuckets": false
    }
  }
}
```

---

## Roadmap

- [x] AWS S3 scanner
- [ ] AWS IAM scanner
- [ ] AWS EC2 / Network scanner
- [ ] HTML report generation (Jinja2)
- [ ] Multi-account support
- [ ] Azure support
- [ ] Automated remediation suggestions
- [ ] ServiceNow integration

---

## Source

[github.com/petorske/infrastructure-security-scanner](https://github.com/petorske/infrastructure-security-scanner)
