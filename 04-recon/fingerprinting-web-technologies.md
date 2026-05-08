# Fingerprinting Web Technologies

Goal: figure out what the target is built with before I start poking at it. The tech stack tells me which bugs are even worth trying.

## What I want to find out

- Web server (Apache, Nginx, IIS, Cloudflare)
- Backend language (PHP, Node, Python, Java, .NET)
- Framework or CMS (WordPress, Laravel, Express, Django)
- CDN or WAF in front of the site
- Security headers — and which ones are missing
- Version numbers, if I can grab them

Old versions are gold. Old version + a quick CVE search gives me a list of known bugs to try.

## Tools

### builtwith.com

Paste a URL, get the stack. Hosting, frameworks, CMS, analytics, payment, fonts, all of it. No setup.

This is my first move because it's passive — the target doesn't see me hit them. builtwith pulls from its own data.

### Wappalyzer (Chrome extension)

Same idea but in my browser. I load the site like a normal user, click the Wappalyzer icon, see the stack.

Faster than builtwith for casual browsing. The downside is my browser actually requests the site, so it's not 100% passive — but it looks like normal traffic.

### curl -I

```
curl -I https://target.tld
```

`-I` does a HEAD request. I get the response headers and nothing else (no HTML body). I'm looking at:

- `Server:` — Apache/2.4.41, nginx/1.18, etc.
- `X-Powered-By:` — PHP/7.4, Express, ASP.NET
- `Content-Length:` — big number means a big page (probably a CMS or lots of assets)
- Security headers (or what's missing)
- `Set-Cookie:` — cookie names like `PHPSESSID`, `JSESSIONID`, `ASP.NET_SessionId` give the backend away

Add `-L` to follow redirects:

```
curl -I -L https://target.tld
```

A lot of sites 301 from `http` to `https`, or apex to `www`. Without `-L` I only see the redirect headers. With `-L` curl follows the chain and shows the real headers at the end.

### securityheaders.com

Paste a URL, get a letter grade on the site's security headers. Quick way to spot what's missing:

- `Content-Security-Policy`
- `Strict-Transport-Security`
- `X-Frame-Options`
- `X-Content-Type-Options`
- `Referrer-Policy`
- `Permissions-Policy`

Missing headers go straight in my notes — those become findings later. An A+ score is useful intel too: it tells me the team cares about web security, so the obvious stuff is probably already handled.

### nmap (Kali)

For port and service info I jump to Kali and run nmap.

```
nmap -p443 -A target.tld
```

- `-p443` — only scan port 443
- `-A` — aggressive scan. Service versions, OS detection, default scripts, traceroute. One flag, lots of output.

For both web ports at once:

```
nmap -p80,443 -A target.tld
```

I check port 80 even when I know the site auto-redirects to 443. Sometimes something is still listening on 80 that the team forgot about.

What I pull out of the nmap output:

- Service version — `nginx 1.14.2`, OpenSSL version, etc. Outdated = check for CVEs.
- TLS cert details — issuer, dates, **subject alternative names**. The SAN list often leaks other subdomains I didn't know about.
- `http-title` — page title from the http-title script
- `http-server-header` — server header
- `http-robots.txt` — sometimes lists disallowed paths I should look at

## My workflow

1. **builtwith.com** — passive stack overview
2. **Wappalyzer** while I browse the site normally
3. **`curl -I -L`** to read the headers myself
4. **securityheaders.com** — header audit, save the report
5. **nmap `-p80,443 -A`** — only after I'm sure I'm in scope

Passive stuff first. Active scans only after I confirm I have authorization. Hitting an out-of-scope host with nmap is how engagements go sideways.

## Why this part matters

Each tool gives me one piece. Stack them together and I end up with something like:

> WordPress 5.8 behind Cloudflare, Apache 2.4.41, MySQL backend, jQuery 3.5.1, missing CSP and HSTS.

That's enough to start looking up known issues for those exact versions and to pick which OWASP categories to focus on first.

Recon is just stacking small facts. One tool at a time.
