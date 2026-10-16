# Sysmon Configuration

This lab uses the community-maintained SwiftOnSecurity Sysmon config:
https://github.com/SwiftOnSecurity/sysmon-config

Rather than vendoring a copy here (it updates frequently and carries its own license),
install it directly:

```powershell
# On the DC, as Administrator
Invoke-WebRequest -Uri https://download.sysinternals.com/files/Sysmon.zip -OutFile Sysmon.zip
Expand-Archive Sysmon.zip -DestinationPath Sysmon
Invoke-WebRequest -Uri https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml -OutFile sysmonconfig.xml
.\Sysmon\Sysmon64.exe -accepteula -i sysmonconfig.xml
```

If your network blocks direct downloads of `raw.githubusercontent.com` (as mine did),
download `sysmonconfig-export.xml` in a browser elsewhere and transfer it in, then run
the install command above pointing at the local file.

## Wiring Sysmon into Wazuh

Add this block to the Wazuh agent's `ossec.conf`, just before `</ossec_config>`:

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

Then restart the agent service:

```powershell
Restart-Service WazuhSvc
```
