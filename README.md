# 🛡️ Realistic Attacker Emulation with Layered Detection Logic

This project reconstructs attacker command chains from fragmented Linux audit logs using Splunk macros, regex extraction, and forensic-grade aggregation. It supports modular dashboards, session-aware correlation, and realistic emulation of adversary behavior.

---

## 🔧 Components

- **Auditd Rules**: Custom rules for capturing `EXECVE` and `SYSCALL` events
- **Splunk Macro**: `reconstruct_command_chain` parses and joins fragmented audit events
- **Dashboard Panel**: Visualizes attacker command chains with metadata
- **Sample Logs** *(optional)*: Anonymized auditd logs for testing and validation

---

## 📦 Macro Logic

```spl
rex max_match=0 field=_raw "a[0-9]+=\"(?<arg>[^\"]+)\""
| eval command_chain=mvjoin(arg, " ")
| rex field=_raw "pid=(?<pid>\d+)"
| rex field=_raw "ppid=(?<ppid>\d+)"
| rex field=_raw "ses=(?<ses>\d+)"
| rex field=_raw "auid=(?<auid>\d+)"
| rex field=_raw "exe=\"(?<exe>[^\"]+)\""
| rex field=_raw "comm=\"(?<comm>[^\"]+)\""
| stats values(command_chain) as command_chain values(exe) as exe values(comm) as comm values(auid) as auid values(ses) as ses by host, pid
