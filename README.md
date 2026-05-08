# TCM Practical Bug Bounty — Notes

My study notes for the [TCM Security Practical Bug Bounty](https://academy.tcm-sec.com/p/practical-bug-bounty) course. The course preps for the [PWPA cert](https://academy.tcm-sec.com/p/practical-web-pentest-associate), which I'm aiming for after.

I'm writing these as I go. Plain markdown, my own words, dated commits. Public so other learners can use them and so my prep is visible.

— Luis Javier Lozoya · [luislozoya.com](https://www.luislozoya.com) · [github.com/Javierlozo](https://github.com/Javierlozo)

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
