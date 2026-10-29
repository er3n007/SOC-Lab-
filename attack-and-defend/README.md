# Attack & Defend

Five Active Directory attack simulations run against the lab domain (`lab.local`),
each paired with how it showed up (or didn't) in Wazuh. Attacks run from Kali;
detection checked in the Wazuh dashboard (Discover + alert rules).

| # | Attack | MITRE ATT&CK | Status |
|---|--------|---------------|--------|
| 1 | [Brute-force / password spray](01-brute-force/) | T1110 | ✅ Done |
| 2 | [Kerberoasting](02-kerberoasting/) | T1558.003 | ✅ Done |
| 3 | [AS-REP Roasting](03-asrep-roasting/) | T1558.004 | ⏳ Planned |
| 4 | [Pass-the-Hash](04-pass-the-hash/) | T1550.002 | ⏳ Planned |
| 5 | [LLMNR/NBT-NS Poisoning](05-llmnr-poisoning/) | T1557.001 | ⏳ Planned |

Each folder has: the exact attack steps, what Wazuh caught (rule ID, alert
description), and a `screenshots/` folder for sanitized evidence (see the
repo-wide [screenshot sanitizing rules](../screenshots/README.md) before adding
any image here).
