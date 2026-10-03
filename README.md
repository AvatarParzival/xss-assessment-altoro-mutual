# XSS Vulnerability Assessment — Altoro Mutual

[![Web Security](https://img.shields.io/badge/Domain-Web%20Application%20Security-CC0000)](https://owasp.org/www-project-top-ten/)
[![XSS](https://img.shields.io/badge/Vulnerability-Cross--Site%20Scripting-F7941E)](https://owasp.org/www-community/attacks/xss/)
[![Burp Suite](https://img.shields.io/badge/Tool-Burp%20Suite%20Professional-FF6633)](https://portswigger.net/burp)
[![OWASP](https://img.shields.io/badge/Framework-OWASP%20Top%2010-000000)](https://owasp.org/www-project-top-ten/)

A junior penetration tester's XSS vulnerability assessment against **Altoro Mutual** (`demo.testfire.net`), a deliberately vulnerable banking web application. One Reflected XSS vulnerability was confirmed with live Burp Suite evidence. Stored and DOM-based XSS were not identified on the tested pages.

> **Core principle:** a finding is only reported when JavaScript execution is directly observed — not inferred from source inspection alone.

## Final Report

📄 **[Read the complete XSS Assessment Report](WEB%20ASSESSMENT%20-%20Zynex.pdf)**

- **Target application:** Altoro Mutual — `https://demo.testfire.net/`
- **Testing date:** 17 July 2026
- **Assessor:** Abdullah Zubair
- **Tools:** Burp Suite Professional, Google Chrome, Browser Developer Tools

## Scope — Input Points Identified

| Input Point | URL | Parameter(s) |
|---|---|---|
| Search bar | `/search.jsp` | `query` |
| Login username | `/login.jsp` | `uid` |
| Login password | `/login.jsp` | `passw` |
| Feedback form | `/feedback.jsp` | `cfile`, `name`, `email_addr`, `subject`, `comments` |
| Subscribe | `/subscribe.jsp` | `txtEmail` |

## Findings

| ID | Type | URL | Parameter | Severity | Result |
|---|---|---|---|---|---|
| XSS-001 | Reflected XSS | `/search.jsp` | `query` | 🟡 Medium | ✅ Confirmed |
| — | Stored XSS | `/feedback.jsp` | multiple | — | ❌ Not identified |
| — | DOM-based XSS | `/search.jsp` | — | — | ❌ Not identified |

### XSS-001 — Reflected XSS

- **Payload:** `query=<script>alert(1)</script>`
- **Reflection location:** Search Results page — value reflected inside HTML response without output encoding
- **Confirmed:** Browser executed injected JavaScript and displayed alert dialog
- **Burp evidence:** HTTP request and response captured showing reflection

**Stored XSS:** The feedback form accepts input but does not redisplay submissions to users — no stored rendering path identified.

**DOM XSS:** No client-side code reading from `location.hash`, `location.search`, cookies, or local storage. No unsafe DOM APIs (`innerHTML`, `document.write()`, `eval()`) present on tested pages.

## Reproduction Steps

1. Open Burp Browser
2. Navigate to `https://demo.testfire.net/search.jsp`
3. Enter `<script>alert(1)</script>` into the search box
4. Submit — observe the input reflected in search results
5. Browser executes injected JavaScript and displays alert containing `1`

## Recommendations

1. Apply context-aware output encoding on all reflected user input
2. Validate and sanitise user input server-side before processing
3. Implement a Content Security Policy (CSP) to restrict script execution
4. Avoid `innerHTML` and `document.write()` — use `textContent` where appropriate
5. Perform regular automated and manual XSS testing as part of the SDLC

## Risk Rating

**Medium.** Successful reflected JavaScript execution was observed. Attackers can exploit this to run arbitrary JavaScript in victims' browsers, enabling phishing, session theft, and unauthorised actions. No Stored or DOM XSS was confirmed.

## Repository Structure

```text
.
├── README.md
└── WEB ASSESSMENT - Zynex.pdf
```

> No loose screenshots are included — all evidence is embedded in the PDF report.

## Limitations

- Testing was performed against `demo.testfire.net`, a deliberately vulnerable training application — findings do not reflect production risk.
- Only the five identified input points were tested; additional parameters or endpoints may exist.
- Stored XSS could not be verified as submitted feedback was not rendered back to any user-accessible location.
- DOM XSS analysis was limited to the pages tested; other pages may contain vulnerable client-side code.

## Lessons Learned

- Output encoding is the single most effective control against reflected XSS — its absence is all that is needed for exploitation.
- Absence of stored rendering is a meaningful negative finding — it narrows the attack surface rather than being a gap in testing.
- Burp Suite request/response capture provides irrefutable evidence of reflection and is essential for professional reporting.
- A CSP header would have blocked alert execution even if the reflection remained — defence in depth matters.

## Author

**Abdullah Zubair**  
Cybersecurity | GRC | Security Automation
- GitHub: [@AvatarParzival](https://github.com/AvatarParzival)
- LinkedIn: [Abdullah Zubair](https://www.linkedin.com/in/abdullahzubairr)
- Email: [abdullah69zubair@gmail.com](abdullah69zubair@gmail.com)

## Responsible Use

This repository is intended for educational, defensive-security and professional portfolio purposes. All testing was performed against a deliberately vulnerable training application. Do not test for XSS vulnerabilities against websites or applications without explicit written authorisation.
