# 2. Kerberoasting

**MITRE ATT&CK:** [T1558.003 — Steal or Forge Kerberos Tickets: Kerberoasting](https://attack.mitre.org/techniques/T1558/003/)
**Status:** Done

## Attack

From Kali, using a known low-privileged domain account (`john.doe`), requested
a Kerberos service ticket for the SQL service account (`svc_sql`, SPN
`MSSQLSvc/sql01.lab.local:1433`):

```bash
impacket-GetUserSPNs lab.local/john.doe:'<password>' -dc-ip <dc-ip> -request -outputfile svc_sql.hash
```

The ticket hash (`$krb5tgs$23$...`) was cracked offline with Hashcat:

```bash
hashcat -m 13100 svc_sql.hash -a 6 /usr/share/wordlists/rockyou.txt ?d?d?d?s -O
```

Cracked in about a minute — a dictionary word (`Passw0rd`) with a 3-digit +
symbol suffix (`123!`), a pattern that satisfies a typical complexity policy
on paper but is trivially guessable.

## Detection

Looked for Event ID **4769** (Kerberos service ticket request) with
`ticketEncryptionType: 0x17` (RC4) — the classic Kerberoasting tell, since
healthy modern traffic should request AES. This specific ticket request
wasn't cleanly visible as its own 4769 in this run (worth revisiting — see
Notes).

What **did** fire: Wazuh's built-in rule **92652** —
*"Successful Remote Logon Detected... NTLM authentication, possible
pass-the-hash attack"* (mapped to T1550.002 / T1078.002) — triggered on the
NTLM-over-LDAP bind that `GetUserSPNs` used to enumerate SPNs, at the same
timestamp as the ticket request. Not a perfectly labeled match for
Kerberoasting specifically, but it's a real, working detection that caught
the attack tooling's activity.

## Evidence

See `screenshots/` (sanitized).

## Notes / Open question

The expected clean `4769` + RC4 match for the `svc_sql` ticket request itself
didn't show up in Discover despite repeated attempts and confirming the
`Audit Kerberos Service Ticket Operations` subcategory. Current theory:
`impacket-GetUserSPNs`'s request flow doesn't always generate a distinct,
easily matched 4769 compared to a pure-Kerberos tool (e.g. Rubeus from a
domain-joined Windows host). A custom Wazuh rule for RC4-encrypted 4769
events is still worth adding for when that scenario is tested — flagged as
a follow-up rather than blocking the rest of the attack/defense series.
