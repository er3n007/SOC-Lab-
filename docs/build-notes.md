# Build Notes

Chronological notes from building the lab. IPs and hostnames below are examples
(`10.x.x.x`, `DC01`) — replace with placeholders if copying into a public post.

## Environment

- Domain Controller: Windows Server (evaluation), promoted to a new forest `lab.local`
- SIEM: Wazuh (OVA deployment), VirtualBox, bridged networking
- Attacker: Kali Linux
- Host: 16 GB RAM laptop, VMs split across two drives for space

## Issues hit and how they were solved

### 1. DC shutting down randomly
**Symptom:** the DC would shut down with no clear trigger.
**Diagnosis:** `Get-WinEvent -FilterHashtable @{LogName='System';Id=1074,...}` showed
Event ID 1074 from `wlms.exe` (Windows License Monitoring Service) — the evaluation
license had expired, and Windows shuts itself down on a timer once that happens.
**Fix:** `slmgr /rearm` followed by a restart (`shutdown /r /t 0`, never `/s`, since a
shutdown on a machine you don't physically control means waiting for someone else to
power it back on).

### 2. Static IP + DNS for the DC
Set a static IP and pointed the DC's own DNS at itself before promoting it — otherwise
AD DS setup and later domain joins get flaky.

### 3. Bridged networking across VMs
The SIEM and the DC ended up on different subnets on the same physical network. Bridged
adapters (not NAT) were required so the Wazuh agent could reach the manager on ports
1514/1515.

### 4. Blocked GitHub raw downloads from the DC
`Invoke-WebRequest` to `raw.githubusercontent.com` from the DC kept failing
("underlying connection was closed") even though `Test-NetConnection` showed the TCP
port was reachable — the network was filtering content, not blocking the connection.
**Workaround:** downloaded the Sysmon config in a browser on another machine and
transferred it in via clipboard/RDP, then installed Sysmon locally.

### 5. Getting Sysmon logs into Wazuh
Sysmon logs to its own Windows Event Log channel
(`Microsoft-Windows-Sysmon/Operational`), which isn't collected by a default Wazuh
agent install. Added a `<localfile>` block to `ossec.conf` pointing at that channel,
then restarted the `WazuhSvc` service. Verified in the Wazuh dashboard by searching
for Sysmon-derived fields and confirming new events were landing.

## Current status

- [x] DC built and stable (license rearmed)
- [x] Wazuh manager/dashboard deployed
- [x] Sysmon installed and logging
- [x] Wazuh agent on the DC, Security + Sysmon channels forwarding
- [x] First detection confirmed end-to-end (logon/logoff events visible in dashboard)
- [ ] Custom detection rules written and mapped to MITRE ATT&CK
- [ ] Additional attack scenarios documented
