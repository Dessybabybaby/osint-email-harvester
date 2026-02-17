# OSINT Email Harvester

> Automated email harvesting tool using n8n to extract, validate, and deduplicate email addresses from public web pages for security research and OSINT investigations

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![n8n](https://img.shields.io/badge/n8n-workflow-orange)](https://n8n.io)

![Workflow Screenshot](media/workflow-screenshot.png)

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Demo](#demo)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Validation Methods](#validation-methods)
- [Sample Data](#sample-data)
- [Ethical Use](#ethical-use)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## Overview

**Problem:** Security researchers and penetration testers manually browse websites to gather email addresses for reconnaissance, phishing simulations, or security awareness training. This process is time-consuming and error-prone.

**Solution:** This n8n workflow automatically fetches public web pages (contact pages, team pages, about pages), extracts all email addresses using regex patterns, validates email format, removes duplicates, enriches with domain information, and exports to CSV for further analysis.

**Technology:**
- n8n (workflow automation)
- HTTP requests (web page fetching and headers)
- Regex pattern matching (obfuscation-aware extraction)
- Email format validation (RFC 5322)
- CSV export (with automated source tracking)

**Use Cases:**
- Security reconnaissance (authorized pentesting)
- OSINT investigations
- Contact database building (with consent)
- Phishing simulation preparation
- Competitive intelligence research

---

## Features

- **Automated web scraping** - Fetch multiple pages in batch
- **Regex extraction** - Find emails in HTML, JavaScript, text
- **Format validation** - RFC 5322 compliant email validation
- **Deduplication** - Remove duplicate entries automatically and illegal characters
- **Domain enrichment** - Extract company domain info
- **Fail-Safe Branching** - IF-Node logic ensures clean separation of errors vs. hits.
- **Rate limiting** - Respect server resources
- **Source tracking** - Persistent URL attribution for every lead found

---

## Demo

### Audio Case Study (Coming Soon)
Full walkthrough available after workflow build.

### Visual Demo
![Demo GIF](media/demo.gif)

---

## Prerequisites

**Required:**
- **n8n instance** (self-hosted or cloud)
- **Target URLs** (public web pages to scan)
- **Internet connection**

**Optional:**
- **Email verification API** (for deeper validation)
- **Proxy service** (for large-scale scraping)

---

## Installation

### Quick Start: Import Workflow (5 minutes)

1. **Download workflow:**
   - [Releases](https://github.com/Dessybabybaby/osint-email-harvester/releases)
   - Download `email-harvester-workflow.json`

2. **Import to n8n:**
   - Workflows → Import from File

3. **Configure target URLs:**
   - Edit "Load Target URLs" node
   - Add URLs to scan

4. **Execute workflow:**
   - Click "Execute Workflow"
   - Review CSV output

5. **Export results:**
   - Download: `file.csv`

---

## Usage

### Setting Target URLs

**Configure URLs to scan:**
```javascript
// In "Load Target URLs" node:
const urls = [
  'https://example.com/contact',
  'https://example.com/about',
  'https://example.com/team',
  // Add more...
];
```

**Common target pages:**
- `/contact`
- `/team`
- `/staff`

### Running Harvester

**Manual Execution:**
1. Open workflow
2. Click "Execute Workflow"
3. Wait for completion
4. Download CSV: `file.csv`

**CSV Output Format:**
```csv
Email,Domain,URL,Date-Found
john@example.com,example.com,https://example.com/contact,2026-01-19
jane@example.com,example.com,https://example.com/team,2026-01-19
```

### Batch Processing

**Multiple domains:**
```javascript
const domains = ['company1.com', 'company2.com', 'company3.com'];
const urls = [];

domains.forEach(domain => {
  urls.push(`https://${domain}/contact`);
  urls.push(`https://${domain}/about`);
  urls.push(`https://${domain}/team`);
});
```

---

## Validation Methods

### Email Regex Pattern

**RFC 5322 Compliant:**
```regex
\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b
```

**Catches:**
- Standard emails: `user@example.com`
- Subdomains: `user@mail.example.com`
- Plus addressing: `user+tag@example.com`
- Dots in local: `first.last@example.com`

**Filters out:**
- Invalid TLDs: `user@example.c`
- Missing @: `userexample.com`
- Spaces: `user @example.com`
- Special chars: `user<>@example.com`

### Format Validation

**Checks:**
1. `(at)` or `[at]` → `@`
2. `(dot)` or `[dot]` → `.`
3. `[space]at[space]` → `@`

### Domain Validation

**Extracts:**
- Root domain: `example.com`
- Subdomain: `mail.example.com`
- TLD: `.com`, `.org`, `.co.uk`

---

## Sample Data

### Test Target Pages

**`sample-data/test-urls.csv`**
```csv
URL,Expected-Emails
https://example.com/contact,2
https://example.org/team,5
```

**`sample-data/harvested-emails-sample.csv`**
```csv
Email,Domain,Source-URL,Date-Found
contact@example.com,example.com,https://example.com/contact,2026-01-19
info@example.com,example.com,https://example.com/contact,2026-01-19
john@example.org,example.org,https://example.org/team,2026-01-19
```

### Running Test

1. Use sample URLs
2. Execute workflow
3. Compare output with sample CSV

---

## Ethical Use

### CRITICAL: Responsible Use Guidelines

**This tool is for AUTHORIZED security research and OSINT only.**

**Permitted Uses:**
- Authorized penetration testing (with written consent)
- Security research on your own organization
- OSINT for legitimate investigations
- Phishing simulations (authorized by organization)
- Academic research (with ethical approval)

**Prohibited Uses:**
- Unauthorized data harvesting
- Spam email campaigns
- Selling harvested email lists
- Harassment or doxxing
- Violating anti-spam laws (CAN-SPAM, GDPR)

### Legal Considerations

**GDPR Compliance:**
- Email addresses are personal data
- Must have legal basis for processing
- Respect data subject rights
- Document lawful processing purpose

**CAN-SPAM Act (US):**
- Don't send commercial emails without consent
- Provide opt-out mechanism
- Include physical address in emails

**Computer Fraud & Abuse Act:**
- Don't bypass access controls
- Respect robots.txt
- Avoid excessive requests (DDoS)

### Best Practices

**Rate Limiting:**
```javascript
// In Wait node - configurable delay
Wait: 5 seconds between batches
Batch Size: 5 URLs per batch
```

**Proxy Rotation:**
```javascript
// In "Set Headers + Random Proxy" node
const proxies = [
  'http://proxy1.company.net:8080',
  'http://proxy2.company.net:8080'
];
```

**Data Handling:**
- Encrypt harvested data at rest
- Failed requests captured in Error Logger
- Errors converted to file and emailed separately
- Delete data when no longer needed
- Don't share without consent
- Secure storage (access controls)

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| **No emails found** | Verify pages contain emails; check regex pattern; inspect HTML source |
| **HTTP 403/429 errors** | Add User-Agent header; implement rate limiting; use proxy |
| **Invalid emails detected** | Tighten regex pattern; add domain validation; filter known invalid patterns |
| **Duplicates in output** | Enable deduplication in workflow; check CSV merge logic |
| **Timeout errors** | Increase HTTP request timeout; reduce batch size |

**Debug Mode:**
```javascript
// Add to extraction node:
console.log('Found emails:', emails);
console.log('Page HTML length:', html.length);
```

---

## Privacy & Data Protection

### Data Minimization

**Only collect necessary data:**
- Email address (required)
- Source URL (for attribution)
- Date found (for freshness)

**Don't collect:**
- Full names (unless necessary)
- Phone numbers
- Physical addresses
- Social media profiles

### Retention Policy

**Recommended:**
- Delete after 90 days
- Review quarterly for relevance
- Remove upon request (GDPR right to erasure)

### Secure Storage
```bash
# Encrypt CSV output:
gpg --encrypt --recipient you@example.com harvested-emails-sample.csv

# Set restrictive permissions:
chmod 600 harvested-emails-sample.csv

# Store in secure location:
mv harvested-emails.csv /secure/encrypted/storage/
```

---

## License

MIT License - see [LICENSE](LICENSE)

**Disclaimer:** This tool is provided for educational and authorized security research purposes only. Users are solely responsible for ensuring their use complies with all applicable laws and regulations.

---

## Acknowledgments

- Inspired by [Mad Hat](https://youtube.com/@MadHat) - OSINT techniques and methodology
- Built with [n8n.io](https://n8n.io)
- Email regex based on RFC 5322 standard

---

## Contact

**Creator:** [Achusi Desmond]
- Portfolio: https://Dessybabybaby.github.io/portfolio-site
- GitHub: [Dessybabybaby](https://github.com/Dessybabybaby)

---

**If this tool aids your security research, please star the repo!**

**Use responsibly. Respect privacy. Follow the law.**
