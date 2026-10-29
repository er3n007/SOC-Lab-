# 3. AS-REP Roasting

**MITRE ATT&CK:** [T1558.004 — Steal or Forge Kerberos Tickets: AS-REP Roasting](https://attack.mitre.org/techniques/T1558/004/)
**Status:** Planned

## Attack (plan)

Request TGTs for accounts that have Kerberos pre-authentication disabled —
no valid credentials needed, just a list of candidate usernames:

```bash
impacket-GetNPUsers lab.local/ -usersfile users.txt -dc-ip <dc-ip> -format hashcat -outputfile asrep.hash
```

If any accounts are vulnerable, crack the resulting hash offline with
Hashcat the same way as the Kerberoasting hash.

## Detection (plan)

Watch for Event ID **4768** (TGT request) where `Pre-Authentication Type` is
`0` — normal logons always show pre-auth type `2` (encrypted timestamp);
`0` means no pre-auth challenge was required, the AS-REP Roasting tell.

## Evidence

_To add once run._
