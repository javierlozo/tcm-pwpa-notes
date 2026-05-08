# TCM Practical Bug Bounty — Notes

My study notes for the [TCM Security Practical Bug Bounty](https://academy.tcm-sec.com/p/practical-bug-bounty) course. The course preps for the [PWPA cert](https://academy.tcm-sec.com/p/practical-web-pentest-associate), which I'm aiming for after.

I'm writing these as I go. Plain markdown, my own words, dated commits. Public so other learners can use them and so my prep is visible.

— Luis Javier Lozoya · [luislozoya.com/notes](https://www.luislozoya.com/notes) · [github.com/Javierlozo](https://github.com/Javierlozo)

> **Currently studying:** 01 · Reconnaissance and Information Gathering
> **Last updated:** May 8, 2026

## Progress

### 01 · Reconnaissance and Information Gathering
- [x] [Fingerprinting Web Technologies](./01-recon/fingerprinting-web-technologies.md)
- [x] [Directory Enumeration and Brute Forcing](./01-recon/directory-enumeration.md)
- [ ] Subdomain Enumeration
- [ ] Burp Suite Overview

### 02 · Authentication and Authorization Attacks
- [ ] Brute-force attacks
- [ ] Attacking MFA
- [ ] IDOR
- [ ] APIs intro
- [ ] Broken access control
- [ ] Testing with Autorize

### 03 · Injection Attacks
- [ ] LFI / RFI
- [ ] SQL injection (basic, blind, second-order)
- [ ] XSS (reflected, stored)
- [ ] Command injection (basic, blind)
- [ ] SSTI
- [ ] XXE
- [ ] Insecure file uploads

### 04 · Automated Tools
- [ ] Automated scanners
- [ ] Scripting and automation

### 05 · Other Common Vulnerabilities
- [x] [Vulnerable Components](./05-other-vulns/vulnerable-components.md) — found 4 CVEs in my own portfolio while writing this
- [ ] CSRF (basic + token bypass)
- [ ] SSRF (basic + blind)
- [ ] Subdomain takeovers
- [ ] Open redirects

### 06 · Reporting
- [ ] CVSS (parts 1 + 2)
- [ ] Writing pentest reports
- [ ] Vulnerability disclosure programs
- [ ] Communicating with clients/triagers

### 07 · Evasion Techniques
- [ ] WAF identification and fingerprinting
- [ ] Input validation bypass

### Lab Writeups
- [ ] First lab writeup coming once I complete one

## Sections

```
01-recon/             Fingerprinting, dir enum, subdomain enum, Burp basics
02-auth-attacks/      Brute force, MFA, IDOR, broken access control, Autorize
03-injection-attacks/ LFI/RFI, SQLi, XSS, command injection, SSTI, XXE, file uploads
04-automated-tools/   Scanners and scripting
05-other-vulns/       CSRF, SSRF, subdomain takeover, open redirect, vuln components
06-reporting/         CVSS, report writing, VDP, triager comms
07-evasion/           WAF identification, input validation bypass
lab-writeups/         Walkthroughs from sanctioned environments only
```

Each folder has a README that lists the files inside.

## Ground rules

- Notes in my own words. Not transcripts of TCM lectures.
- Lab writeups only from places I'm authorized to test: TCM labs, PortSwigger Academy, HackTheBox, my own homelab.
- No real client work. No embargoed bug bounty findings.

## Disclaimer

Personal notes. Not affiliated with TCM Security. Anything in here is meant for use in lab environments I own or have permission to test.

## License

MIT. If they help, a star or a link is appreciated.
