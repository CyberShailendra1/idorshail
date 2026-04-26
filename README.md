<div align="center">

```
 ██╗██████╗  ██████╗ ██████╗ ███████╗██╗  ██╗ █████╗ ██╗██╗     
 ██║██╔══██╗██╔═══██╗██╔══██╗██╔════╝██║  ██║██╔══██╗██║██║     
 ██║██║  ██║██║   ██║██████╔╝███████╗███████║███████║██║██║     
 ██║██║  ██║██║   ██║██╔══██╗╚════██║██╔══██║██╔══██║██║██║     
 ██║██████╔╝╚██████╔╝██║  ██║███████║██║  ██║██║  ██║██║███████╗
 ╚═╝╚═════╝  ╚═════╝ ╚═╝  ╚═╝╚══════╝╚═╝  ╚═╝╚═╝  ╚═╝╚═╝╚══════╝
```

**IDOR Active Scanner — Powered by AI**

[![Version](https://img.shields.io/badge/version-1.0.0-00ff88?style=for-the-badge&logo=github)](#)
[![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge)](#)
[![Python](https://img.shields.io/badge/python-3.8%2B-yellow?style=for-the-badge&logo=python)](#)
[![Status](https://img.shields.io/badge/status-active-brightgreen?style=for-the-badge)](#)
[![Ethical Use](https://img.shields.io/badge/ethical%20use-only-red?style=for-the-badge)](#)

*A fast, AI-assisted Insecure Direct Object Reference (IDOR) vulnerability scanner built for authorized penetration testing and bug bounty hunting.*

</div>

---

## ⚠️ Legal Disclaimer

> **This tool is for authorized security testing ONLY.**
> Using this tool against systems without explicit written permission is **illegal** and may violate laws including the Computer Fraud and Abuse Act (CFAA), the UK Computer Misuse Act, and similar legislation worldwide.
> The developers of `idorshail` assume **no liability** for misuse. Always get written authorization before testing any system.

---

## 📖 Table of Contents

- [What is IDOR?](#-what-is-idor)
- [Features](#-features)
- [How It Works](#-how-it-works)
- [Installation](#-installation)
- [Usage](#-usage)
- [Configuration Options](#-configuration-options)
- [Output & Results](#-output--results)
- [Example Scenarios](#-example-scenarios)
- [IDOR Cheat Sheet](#-idor-cheat-sheet)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🔍 What is IDOR?

**Insecure Direct Object Reference (IDOR)** is a type of access control vulnerability that occurs when an application uses user-controllable input to access objects directly — without verifying whether the user is authorized to access them.

### Classic Example

```http
GET /api/users/1042/invoice HTTP/1.1
Host: target.com
Authorization: Bearer eyJ...your_token
```

If changing `1042` to `1043` returns another user's invoice — that's an **IDOR**.

### Why IDOR is Critical

| Severity | Impact |
|----------|--------|
| 🔴 High | Access to other users' PII, financial records |
| 🔴 High | Unauthorized modification or deletion of data |
| 🟠 Medium | Privilege escalation via object reference manipulation |
| 🟠 Medium | Mass data enumeration / scraping |

IDOR consistently appears in the **OWASP Top 10** under *Broken Access Control* — the #1 web application security risk.

---

## ✨ Features

- 🤖 **AI-Powered Analysis** — Uses Claude AI to intelligently assess IDOR likelihood per endpoint
- ⚡ **Active Scanning** — Real HTTP request enumeration over configurable ID ranges
- 🎯 **Flexible Targeting** — Supports GET, POST, PUT, DELETE, PATCH methods
- 🔑 **Auth Header Injection** — Bearer tokens, API keys, custom headers
- 🍪 **Cookie Support** — Session-based auth testing
- 📊 **Rich Terminal Output** — Color-coded, timestamped logs with severity tagging
- 📋 **Detailed Reports** — Per-request breakdown: status code, latency, response size, finding
- 🛑 **Abort Control** — Mid-scan cancellation without losing results
- 📁 **Export Support** — JSON and CSV report export

---

## ⚙️ How It Works

```
┌─────────────────────────────────────────────────────┐
│                   idorshail Engine                  │
│                                                     │
│  Config Input                                       │
│  ┌──────────┐    ┌──────────────┐                  │
│  │ Target   │───▶│  ID Iterator │                  │
│  │ URL      │    │  (start→end) │                  │
│  │ Headers  │    └──────┬───────┘                  │
│  │ Cookies  │           │                          │
│  └──────────┘           ▼                          │
│                  ┌──────────────┐                  │
│                  │ HTTP Request │                  │
│                  │  Dispatcher  │                  │
│                  └──────┬───────┘                  │
│                         │                          │
│                         ▼                          │
│                  ┌──────────────┐                  │
│                  │  AI Analysis │ ◀── Claude API   │
│                  │  (IDOR det.) │                  │
│                  └──────┬───────┘                  │
│                         │                          │
│                         ▼                          │
│                  ┌──────────────┐                  │
│                  │  Results &   │                  │
│                  │  Report Gen  │                  │
│                  └──────────────┘                  │
└─────────────────────────────────────────────────────┘
```

The scanner works in three stages:

1. **Enumeration** — Iterates through a user-defined range of IDs, substituting them into the target URL template.
2. **Detection** — Analyzes HTTP responses for IDOR indicators: unexpected `200 OK` on foreign IDs, response body differences, sensitive data leakage.
3. **Reporting** — Classifies findings by severity, captures request/response evidence, and generates a structured report.

---

## 📦 Installation

### Requirements

- Python 3.8+
- pip
- An active internet connection

### Clone & Install

```bash
https://github.com/CyberShailendra1/idorshail.git
cd idorshail
pip install -r requirements.txt
```

### Requirements File (`requirements.txt`)

```
requests>=2.31.0
anthropic>=0.25.0
colorama>=0.4.6
rich>=13.7.0
tqdm>=4.66.0
argparse
json5
```

---

## 🚀 Usage

### Basic Scan

```bash
python idorshail.py -u "https://api.target.com/users/{id}/profile" \
                    --start 1 \
                    --end 100
```

### Authenticated Scan

```bash
python idorshail.py -u "https://api.target.com/orders/{id}" \
                    -m GET \
                    --start 1000 \
                    --end 1050 \
                    -H "Authorization: Bearer YOUR_TOKEN_HERE" \
                    -c "session=abc123; csrf=xyz456"
```

### POST Request with Body

```bash
python idorshail.py -u "https://api.target.com/messages" \
                    -m POST \
                    --param "message_id" \
                    --start 1 \
                    --end 50 \
                    -H "Content-Type: application/json" \
                    -H "Authorization: Bearer TOKEN" \
                    --body '{"message_id": "{id}", "action": "read"}'
```

### Full Options

```bash
python idorshail.py --help
```

```
usage: idorshail.py [-h] -u URL [-m METHOD] [--param PARAM]
                    [--start START] [--end END]
                    [-H HEADER] [-c COOKIES] [--body BODY]
                    [--delay DELAY] [--threads THREADS]
                    [--output OUTPUT] [--format {json,csv,txt}]
                    [--verbose] [--no-ai]

idorshail — IDOR Active Scanner

options:
  -h, --help            Show this help message and exit

Target:
  -u URL                Target URL with {id} placeholder
  -m METHOD             HTTP method (GET, POST, PUT, DELETE, PATCH)
  --param PARAM         Parameter name to fuzz (default: id)
  --start START         Start of ID range
  --end END             End of ID range

Authentication:
  -H HEADER             Custom header (can be used multiple times)
  -c COOKIES            Cookie string
  --body BODY           POST/PUT request body (use {id} placeholder)

Performance:
  --delay DELAY         Delay between requests in ms (default: 300)
  --threads THREADS     Concurrent threads (default: 1)

Output:
  --output OUTPUT       Output file path
  --format FORMAT       Report format: json, csv, or txt
  --verbose             Enable verbose output
  --no-ai               Skip AI analysis (faster, less accurate)
```

---

## ⚙️ Configuration Options

### URL Templates

Use `{id}` or `{paramName}` as the placeholder in your target URL:

```
https://api.target.com/users/{id}/profile         ✅
https://api.target.com/invoice?doc_id={id}         ✅
https://api.target.com/files/{id}/download         ✅
```

### ID Range Strategy

| Strategy | When to Use |
|----------|-------------|
| `--start 1 --end 100` | General user ID enumeration |
| `--start 1000 --end 1100` | Testing ID ranges near your own account |
| `--start 0 --end 10` | Admin/system ID testing (low IDs) |
| `--start 99990 --end 100010` | Testing around a known ID |

### Headers Format

Multiple `-H` flags are supported:

```bash
-H "Authorization: Bearer TOKEN" \
-H "X-User-Role: user" \
-H "Accept: application/json"
```

---

## 📊 Output & Results

### Terminal Output

```
08:42:13 [*] Initializing idorshail v1.0.0
08:42:13 [*] Target: https://api.target.com/users/{id}/profile
08:42:13 [*] Method: GET | Range: 1 → 20
08:42:14 [+] ID=1  → HTTP 200 — safe (own resource)
08:42:14 [+] ID=2  → HTTP 403 — safe (forbidden)
08:42:15 [VULN] ID=3 → HTTP 200 — IDOR detected: accessed another user's profile
08:42:15 [+] ID=4  → HTTP 404 — safe (not found)
08:42:16 [VULN] ID=7 → HTTP 200 — IDOR detected: leaked PII in response body
```

### JSON Report (`--format json`)

```json
{
  "scan_meta": {
    "tool": "idorshail",
    "target": "https://api.target.com/users/{id}/profile",
    "method": "GET",
    "range": "1-20",
    "timestamp": "2025-04-26T08:42:00Z"
  },
  "summary": {
    "total_requests": 20,
    "vulnerabilities": 3,
    "safe": 17,
    "vuln_rate": "15%"
  },
  "findings": [
    {
      "id": 3,
      "url": "https://api.target.com/users/3/profile",
      "status": 200,
      "vulnerable": true,
      "finding": "Accessed another user's profile data without authorization",
      "evidence": { "response_snippet": "{\"name\":\"Jane Doe\",\"email\":...}", "latency_ms": 143, "size_bytes": 892 }
    }
  ]
}
```

---

## 🧪 Example Scenarios

### Scenario 1 — User Profile IDOR

```bash
# You are logged in as user ID 5042
# Test whether you can access other users' profiles

python idorshail.py \
  -u "https://app.target.com/api/profile/{id}" \
  --start 5000 \
  --end 5100 \
  -H "Authorization: Bearer YOUR_JWT" \
  --output profile_idor.json --format json
```

### Scenario 2 — Invoice/Document IDOR

```bash
# Test document access control
python idorshail.py \
  -u "https://app.target.com/api/invoice/{id}/download" \
  --start 1 \
  --end 200 \
  -H "Authorization: Bearer YOUR_JWT" \
  -c "session=abc123"
```

### Scenario 3 — Admin Panel Object Reference

```bash
# Test admin-only resources with a regular user token
python idorshail.py \
  -u "https://admin.target.com/users/{id}/settings" \
  --start 1 \
  --end 50 \
  -H "Authorization: Bearer REGULAR_USER_TOKEN" \
  --verbose
```

### Scenario 4 — POST Body Parameter Fuzzing

```bash
python idorshail.py \
  -u "https://api.target.com/messages/fetch" \
  -m POST \
  --body '{"thread_id": "{id}"}' \
  --start 100 \
  --end 200 \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json"
```

---

## 📚 IDOR Cheat Sheet

### Common IDOR-Prone Endpoints

```
GET  /api/users/{id}
GET  /api/orders/{id}
GET  /api/files/{id}/download
GET  /api/messages/{id}
GET  /api/invoices/{id}
POST /api/admin/user/{id}/delete
PUT  /api/profile/{id}/update
GET  /api/account/{id}/transactions
GET  /report?doc_id={id}
GET  /export?job_id={id}
```

### Response Indicators of IDOR

| Response | Meaning |
|----------|---------|
| `200 OK` on a foreign ID | ✅ Likely IDOR |
| Larger response body than expected | ✅ Possible data leak |
| `403 Forbidden` | ✅ Access control working |
| `404 Not Found` | ✅ Object doesn't exist |
| `401 Unauthorized` | ✅ Auth check working |
| Same response for all IDs | ⚠️ Investigate further |

### Bypasses to Try Manually

```
# Encoding tricks
/api/users/1     →  /api/users/01
/api/users/1     →  /api/users/1.0
/api/users/1     →  /api/users/0x01

# Parameter pollution
/api/order?id=YOURS&id=VICTIM

# HTTP method switching
GET → POST, PUT → PATCH
```

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/new-detection`
3. Commit your changes: `git commit -m 'Add new IDOR detection heuristic'`
4. Push to the branch: `git push origin feature/new-detection`
5. Open a Pull Request

### Roadmap

- [ ] Blind IDOR detection via time-based analysis
- [ ] GraphQL object ID fuzzing support
- [ ] Burp Suite extension integration
- [ ] Nuclei template export
- [ ] UUID/GUID-based IDOR detection
- [ ] Slack/Discord webhook notifications
- [ ] Docker image

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<div align="center">

Made with 🔴 by **[@idorshail](https://github.com/cybershailendra1)**

*Find bugs. Get bounties. Stay ethical.*

</div>
