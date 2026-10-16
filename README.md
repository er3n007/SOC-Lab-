<<<<<<< HEAD
# Mini SOC Lab

A small Security Operations Center (SOC) lab built to get hands-on with the full
detection pipeline, from endpoint telemetry to SIEM to alerting, rather than just
reading about it.

## Architecture

```
          Isolated / controlled lab network
┌────────────┐      ┌──────────────┐      ┌────────────────┐
│   Kali      │      │   AD Domain   │      │   Wazuh SIEM    │
│  (attacker) │◀────▶│  Controller   │─────▶│  (manager +     │
│             │      │  lab.local    │      │   dashboard)    │
└────────────┘      │  Sysmon +     │      └────────────────┘
                     │  Wazuh agent  │
                     └──────────────┘
```

- **Domain Controller:** Windows Server, promoted to a new forest `lab.local`
- **Endpoint telemetry:** Sysmon (SwiftOnSecurity config) + native Windows Security log
- **Log shipping:** Wazuh agent on the DC, forwarding the Security channel and the
  Sysmon operational channel
- **SIEM:** Wazuh manager + dashboard (OVA deployment)
- **Attacker machine:** Kali Linux, set up and isolated on the lab network

## What's in this repo

| Folder | Contents |
|---|---|
| `docs/` | Build notes and troubleshooting log from setting up the lab |
| `sysmon-config/` | How Sysmon is configured and wired into Wazuh |
| `screenshots/` | Sanitized screenshots of the dashboard (no real IPs) |

## Build highlights

A few of the real problems solved while building this (details in `docs/build-notes.md`):

- Diagnosing a Windows Server evaluation license expiry that was silently shutting the
  DC down, found via Event ID 1074 in the System log
- Bridging VM networking across subnets so the SIEM and the DC could reach each other
- Working around a network that blocked direct `raw.githubusercontent.com` downloads
  from the DC, by fetching config files through a browser and transferring them in
- Getting Sysmon + the Windows Security event channel flowing into Wazuh through the
  agent config (`ossec.conf`)

## Status

The core pipeline is up and verified end to end: the DC is stable, Sysmon is logging,
and the Wazuh agent is shipping both the Windows Security log and the Sysmon channel
into the dashboard in real time.

Next: attack simulations and custom detection rules, covered in a follow-up.

## Disclaimer

This lab runs entirely on an isolated/controlled network. No real credentials, IP
addresses, or organizational details are included in this repository.
=======
# SOC-Lab-
>>>>>>> 98fa0295f68222d1a6bbd57acae8a749a2d7817e
