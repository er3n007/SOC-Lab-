# Lab Build Guide

A step-by-step walkthrough of how this lab was built: an Active Directory domain
controller, Sysmon for endpoint telemetry, and a Wazuh SIEM, with Kali as the attacker
machine. Written so someone starting from zero can follow the same path.

IPs and hostnames below are examples (`10.x.x.x`, `DC01`, `lab.local`) — swap in your
own.

## What you need

- A hypervisor: VirtualBox or VMware Workstation (free)
- A Windows Server evaluation ISO (Microsoft's Evaluation Center, free, 180 days)
- The Wazuh OVA (free, prebuilt VM with the manager + dashboard)
- A Kali Linux VM or install
- Roughly 16 GB RAM and 60-80 GB free disk across your machine(s) to run everything
  comfortably

## 1. Plan the network

Decide on one network all three machines will share:
- The domain controller (DC)
- The Wazuh SIEM
- The Kali attacker box

Use **Bridged networking** in your hypervisor for each VM, not NAT. Bridged mode gives
each VM its own address on your real network, so they can all reach each other
directly, which NAT does not allow by default.

## 2. Build the domain controller

1. Install Windows Server (Standard, evaluation edition is fine for a lab) on a VM.
2. Give it a **static IP address** and set its own **DNS server to itself**
   (`127.0.0.1` or its own IP). This step matters: AD relies on DNS, and a DC that
   doesn't resolve its own domain will misbehave later.
3. In Server Manager, install the **Active Directory Domain Services** role.
4. Promote the server to a domain controller:
   - Choose **Add a new forest**
   - Pick a root domain name, e.g. `lab.local`
   - Set a Directory Services Restore Mode (DSRM) password and keep it safe
   - Let the wizard finish; the server will restart

After the restart, confirm it worked:
```powershell
Get-ADDomain
dcdiag /q
```

## 3. Deploy Sysmon on the DC

Sysmon gives much richer endpoint visibility than the default Windows event log
(process creation with command lines, network connections, and more).

1. Download Sysmon from Microsoft Sysinternals.
2. Download a community Sysmon configuration — the SwiftOnSecurity config
   (`github.com/SwiftOnSecurity/sysmon-config`) is a well-maintained starting point.
3. Install Sysmon with that config:
   ```powershell
   .\Sysmon64.exe -accepteula -i sysmonconfig-export.xml
   ```
4. Confirm it's running:
   ```powershell
   Get-Service Sysmon64
   ```

## 4. Deploy the Wazuh SIEM

1. Import the Wazuh OVA into your hypervisor as a new VM.
2. Set its network adapter to Bridged, same network as the DC.
3. Boot it and note its IP address (`ip -4 a` from inside the VM).
4. Open the Wazuh dashboard in a browser at `https://<wazuh-vm-ip>` and log in.

## 5. Connect the DC to Wazuh as an agent

1. In the Wazuh dashboard, go to **Agents → Deploy new agent**.
2. Choose Windows, enter the Wazuh server's IP, and give the agent a name (e.g.
   `DC01`).
3. Run the generated install command on the DC, then start the agent service.
4. Edit the agent's config (`ossec.conf`) to also collect the Sysmon log channel, by
   adding:
   ```xml
   <localfile>
     <location>Microsoft-Windows-Sysmon/Operational</location>
     <log_format>eventchannel</log_format>
   </localfile>
   ```
5. Restart the agent service and confirm in the dashboard that the agent shows as
   **Active** and events are arriving.

## 6. Set up the attacker machine

1. Install or import a Kali Linux VM.
2. Put it on the same bridged network as the DC and the SIEM.
3. Confirm it can reach the DC (`ping` the DC's IP).

At this point the pipeline is complete: endpoint activity on the DC → Sysmon +
Windows Security log → Wazuh agent → Wazuh manager → dashboard, with Kali in place
to generate activity to detect.

## Verifying the pipeline works

Before moving on to anything else, confirm logs are actually flowing:
1. Trigger any ordinary action on the DC (log in, log out, open an app).
2. Search the Wazuh dashboard (**Discover / Threat Hunting**) for that agent's name.
3. Confirm new events appear within a minute or two, and that both Windows Security
   events and Sysmon events are present.

## Current status

- [x] Domain controller built and stable
- [x] Sysmon installed and logging
- [x] Wazuh manager/dashboard deployed
- [x] Wazuh agent on the DC, Security + Sysmon channels forwarding
- [x] Pipeline verified end-to-end (live events visible in the dashboard)
