# SSH Honeypot — Cowrie on Raspberry Pi

A medium-interaction SSH honeypot deployed on a Raspberry Pi to 
capture and analyze simulated attacker behavior.

## What I Built
- Cowrie honeypot running on a dedicated low-privilege service account
- Custom fake hostname to make the target look like a real production box
- Listening on port 2222 (local network only, not internet-exposed)
- Full session capture: login attempts, commands run, and replayable TTY logs

## Setup Overview
1. Created a dedicated, unprivileged `cowrie` service account 
   (least-privilege principle — honeypot doesn't run as root or as my own user)
2. Installed Cowrie in an isolated Python virtual environment
3. Configured `etc/cowrie.cfg` with a custom hostname to increase realism
4. Started the service and verified it was listening on port 2222:
   `cowrie start` / `cowrie status`

## Testing the Honeypot
Connected to the honeypot as if I were an attacker:

ssh root@<pi-ip> -p 2222

Ran a handful of common recon commands (`ls`, `whoami`, `cat /etc/passwd`, 
`uname -a`) to generate realistic session data.

## What Cowrie Captured
- Full login attempt, including the exact credentials used
- An SSH client fingerprint (hassh) — used in real threat intel to 
  cluster and identify attacker tooling
- Every command executed, in order, with timestamps
- A full TTY session recording, replayable with `playlog`:

playlog var/lib/cowrie/tty/<session-id>

- Structured JSON output (`cowrie.json`) suitable for ingestion into 
  a SIEM (planned next step: Splunk)

## Design Decisions
- **Kept local-only (no port forwarding to the internet).** Exposing 
  a honeypot externally is a meaningful step up in risk and scope — 
  wanted to fully understand the tool on my own network first.
- **Left real SSH (port 22) untouched.** Considered remapping ports 
  so the honeypot sits on 22, but held off since there's no benefit 
  without internet exposure, and it adds lockout risk for no reason.
- **Dedicated service account**, not root or my admin user — standard 
  least-privilege practice for any service, especially one designed 
  to attract malicious activity.

## What's Next
- Ingest Cowrie's JSON logs into Splunk
- Build detections/alerts around brute-force patterns and specific 
  command sequences
- Map observed behavior to MITRE ATT&CK techniques

## Tools Used
Cowrie, Raspberry Pi OS Lite, Python (venv), SSH# homelab-honeypot
README for cowrie honyepot
