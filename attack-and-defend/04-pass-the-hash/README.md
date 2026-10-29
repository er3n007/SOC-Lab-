# 4. Pass-the-Hash

**MITRE ATT&CK:** [T1550.002 — Use Alternate Authentication Material: Pass the Hash](https://attack.mitre.org/techniques/T1550/002/)
**Status:** Planned

## Attack (plan)

Using an NTLM hash (captured from an earlier step, or dumped via
`secretsdump.py` against a compromised host), authenticate to the DC without
ever knowing the plaintext password:

```bash
crackmapexec smb <dc-ip> -u <user> -H <ntlm-hash> -d lab.local
```

or move laterally with `psexec.py`/`wmiexec.py` using the same hash.

## Detection (plan)

Watch Event ID **4624** (successful logon) for `AuthenticationPackageName:
NTLM`, `LogonType: 3` (network), with a **blank workstation name** — a
signature of hash-based tooling rather than a real interactive Windows
client. Wazuh's built-in rule **92652** already flags this pattern (seen
firing once already, during Kerberoasting recon in section 2 — worth
re-triggering here deliberately).

## Evidence

_To add once run._
