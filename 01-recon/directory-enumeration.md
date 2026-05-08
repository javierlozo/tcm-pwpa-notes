# Directory Enumeration and Brute Forcing

The point is to find pages, files, and folders the site doesn't link to from the homepage. Old admin panels, leftover dev folders, backups, hidden API endpoints. Stuff the team forgot was still reachable.

It's wordlist-based. Take a list of common names, fire each one at the target, log whatever responds.

## Methodology, not tools

Something the course made a point of: focus on the methodology, not the tool. Tools change. ffuf might be the hot one today and something else next year. What I care about is the thinking. Which response codes mean something, which wordlist to pick, when to turn on recursion. That part stays the same no matter which tool is in fashion.

## Status codes I care about

- `200` — page exists and returns content
- `301` / `302` — redirect, often to a login or to the real path
- `401` / `403` — auth required, but the path itself is real (interesting)
- `404` — doesn't exist
- `500` — server error, sometimes leaks stack traces

200s are the obvious wins. 403s tell me a path is real but I'm locked out, which is usually the first thing I want to dig into.

## ffuf

"Fuzz Faster U Fool." Fast directory and file brute forcer.

```
ffuf -w /usr/share/wordlists/dirb/common.txt:FUZZ -u https://target.tld/FUZZ
```

I pick a wordlist, label it (`:FUZZ`), then drop `FUZZ` into the URL where each word gets swapped in. ffuf does the rest.

Flags I actually use:

- `-recursion` — once it finds a directory, brute force inside it too
- `-recursion-depth N` — cap how deep it goes
- `-fc 404` — filter out 404s from the output
- `-fc 403,404` — multiple codes at once
- `-mc 200,301` — match only these codes (whitelist)
- `-t N` — threads (more = faster but louder)
- `-e .php,.html,.bak` — try each word with these extensions tacked on

Quick rule for filter vs match: `-fc` removes the noise, `-mc` only shows what I want. I use `-mc` when I know what I'm hunting and `-fc` when I'm just exploring.

## dirb

Older tool, simpler.

```
dirb http://target.tld
```

Uses its own wordlist by default (`/usr/share/dirb/wordlists/common.txt`). I can pass my own:

```
dirb http://target.tld /usr/share/wordlists/dirb/big.txt
```

Test specific file extensions:

```
dirb http://target.tld -X .html,.php
```

## dirbuster

GUI version, comes with Kali. Useful when I want to see the result tree visually instead of scrolling terminal output.

What I usually set:

- Target URL
- Work method: Auto Switch (HEAD then GET)
- Threads: 200 with "Go Faster" on
- Wordlist: `/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt`
- Brute Force Dirs + Brute Force Files both checked
- Be Recursive on
- File extensions: `php, html` (and `aspx` for IIS, `jsp` for Java)

The result tree updates live. Easier to spot a branch worth looking at than reading line-by-line.

## Wordlists

Where they live on Kali:

- `/usr/share/wordlists/dirb/` — common, big, small, plus some categorized lists
- `/usr/share/wordlists/dirbuster/` — directory-list-2.3-small / medium / large
- `/usr/share/seclists/Discovery/Web-Content/` — SecLists, the one I reach for most

How I pick:

- Small list first to get a quick read on the target
- Medium for the main pass
- Large only if medium came up empty or I need to be thorough
- Stack-specific lists (`wordpress.txt`, etc.) once I already know what's running

## Threads and noise

More threads = faster scan, more chance of getting flagged. On a real engagement I think about WAF and rate limits before cranking it up.

If the target pushes back (429s, sudden block, IP gets dropped), I slow the scan. Better to take 30 minutes and finish than blast through and get cut off at 60%.

## My order

1. ffuf with a small wordlist, `-mc 200,301,401,403`, no recursion. Quick first pass.
2. Write down anything that responded with something interesting.
3. Re-run with a medium wordlist, add `-recursion -recursion-depth 2` for the promising paths.
4. Try common extensions for the stack: `-e .php,.bak,.old,.zip,.txt`.
5. dirbuster GUI if I want to see the structure as a tree.

Each hit goes in my notes with status code, path, and one line on why it caught my eye.
