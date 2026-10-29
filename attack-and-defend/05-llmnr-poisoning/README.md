# 5. LLMNR / NBT-NS Poisoning

**MITRE ATT&CK:** [T1557.001 — Adversary-in-the-Middle: LLMNR/NBT-NS Poisoning and SMB Relay](https://attack.mitre.org/techniques/T1557/001/)
**Status:** Planned

## Attack (plan)

Run Responder on Kali, on the same broadcast segment as the DC/clients, and
wait for a name-resolution fallback (e.g. a mistyped share name) to trigger
a victim into authenticating to the attacker machine:

```bash
sudo responder -I eth0 -wrf
```

Any captured NetNTLMv2 hash gets cracked offline with Hashcat.

## Detection (plan)

Unlike the other four, this is a network-layer attack rather than an
AD-logon-based one. Detection approach: Sysmon Event ID **3** (network
connection) on hosts, filtered for UDP traffic on **port 5355** (LLMNR) or
**port 137** (NBT-NS) to/from unexpected hosts. May also show up indirectly
as an NTLM logon (4624) to the real target from a host that shouldn't be
initiating it.

## Evidence

_To add once run._
