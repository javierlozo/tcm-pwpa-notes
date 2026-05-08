# Fingerprinting Web Technologies

Before I start poking at a site, I want to know what it's running. The stack tells me which bugs are even worth trying. Old versions plus a quick CVE lookup is often the fastest path to a real finding.

Stuff I'm trying to identify:

- Web server (Apache, Nginx, IIS, Cloudflare in front, etc.)
- Backend language (PHP, Node, Python, Java, .NET)
- Framework or CMS (WordPress, Laravel, Express, Django)
- CDN or WAF in front of the app
- Security headers, and which ones are missing
- Version numbers if I can grab them

## builtwith.com

Paste a URL, get the stack. Hosting, frameworks, CMS, analytics, payment, fonts, all of it. No setup.

This is my first stop because it's passive. The target doesn't see my traffic. builtwith pulls from its own dataset.

## Wappalyzer (Chrome extension)

Same idea but live in my browser. I open the site like a regular user, click the Wappalyzer icon, see the stack.

It's faster than builtwith for casual browsing. Heads up: my browser actually requests the site, so it's not 100% passive. Still looks like normal traffic though.

## curl -I

```
curl -I https://target.tld
```

`-I` does a HEAD request. I get just the response headers, no HTML body. The headers alone tell me a lot.

What I'm reading off the response:

- `Server:` — `Apache/2.4.41`, `nginx/1.18`, etc.
- `X-Powered-By:` — `PHP/7.4`, `Express`, `ASP.NET`
- `Content-Length:` — big number usually means a CMS or a fat page
- Security headers (or what's missing — see below)
- `Set-Cookie:` — cookie names like `PHPSESSID`, `JSESSIONID`, `ASP.NET_SessionId` give the backend away even when nothing else does

Add `-L` to follow redirects:

```
curl -I -L https://target.tld
```

A lot of sites 301 from `http` to `https`, or apex to `www`. Without `-L` I only see the redirect. With `-L` curl walks the chain and shows me the headers from the real destination.

## securityheaders.com

Paste a URL, get a letter grade on the site's security headers. Quick way to see what's missing:

- `Content-Security-Policy`
- `Strict-Transport-Security`
- `X-Frame-Options`
- `X-Content-Type-Options`
- `Referrer-Policy`
- `Permissions-Policy`

Missing headers go straight in my report draft. An A+ score is also useful intel. It tells me the team cares about web hygiene, so the obvious low-hanging stuff is probably already locked down.

## nmap (Kali)

For ports and service versions I move to Kali and run nmap.

Single port:

```
nmap -p443 -A target.tld
```

`-p443` scans only port 443. `-A` is the aggressive flag: service version detection, OS detection, default scripts, traceroute. One flag, lots of output.

Both web ports:

```
nmap -p80,443 -A target.tld
```

I always check port 80 even when the site auto-redirects. Sometimes there's something still listening on 80 that the team forgot about.

What I pull out of nmap:

- Service version. `nginx 1.14.2`, OpenSSL version, etc. Outdated versions are the hook for a CVE search.
- TLS cert details. Issuer, dates, and the subject alt names. The SAN list often leaks subdomains I didn't already know about.
- `http-title` from the http-title script. Page title.
- `http-server-header`. Server header.
- `http-robots.txt` if available. Disallowed paths are usually the interesting ones.

## My order

1. builtwith.com. Passive, gives me the stack overview.
2. Wappalyzer while I browse the site like a normal user.
3. `curl -I -L` to read the headers myself.
4. securityheaders.com for the header report.
5. `nmap -p80,443 -A` once I'm sure I'm in scope.

Passive stuff first. nmap and anything active only after I've confirmed authorization. Hitting an out-of-scope host is how things go wrong fast.

After all of this I usually have a sentence like "WordPress 5.8 behind Cloudflare on Apache 2.4.41, MySQL backend, jQuery 3.5.1, no CSP or HSTS." That's enough to start picking which bugs to look for next.
