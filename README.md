# LockBit Ransomware — Memory Forensics & Incident Response

## Overview

A Windows 7 SP1 workstation exhibited signs of ransomware infection. Given a raw VMware memory snapshot (`.vmem`), this investigation reconstructs the full attack timeline — from initial execution to lateral movement — using memory forensics alone (no disk image was provided).

**Skills demonstrated:** memory forensics (Volatility 3), malware triage, threat intelligence correlation (VirusTotal), Windows internals (processes, registry, network state), incident response planning (NIST-aligned).

## Key Findings

| | |
|---|---|
| **Malware** | `mal.exe` — identified as **LockBit** ransomware (61/69 AV vendors on VirusTotal) |
| **SHA-256** | `5988e75518b2f365671dc49da18b5a70274351721f1f3a8f8f7bf32984e4024c` |
| **Initial execution** | `C:\Users\Josh\Desktop\mal.exe`, PID 900, 2023-04-13 10:06:45 UTC |
| **Persistence** | Registry Run key (`XO1XADpO01`) added in the same second as execution |
| **Impact** | 1,488+ files encrypted with `.lockbit` extension within ~23 seconds |
| **Lateral movement** | 200+ outbound connection attempts to local subnet via TCP 135/445 |
| **Anti-recovery** | Shadow copies and Windows Backup catalog deleted via `vssadmin`/`wbadmin` |

## Methodology

1. **Triage** — established OS profile and user context (`windows.info`, `windows.sessions`, `windows.getsids`)
2. **Process analysis** — identified the malicious process via `windows.pslist` / `pstree` / `psscan`, flagged by anomalous thread count (267) and execution path
3. **Malware identification** — extracted the executable from memory (`windows.dumpfiles`), hashed it, and cross-referenced with VirusTotal for family attribution and behavioral analysis
4. **Persistence & impact analysis** — located the registry autorun key and enumerated encrypted files via `windows.filescan`
5. **Network analysis** — used `windows.netscan` to uncover an active lateral-movement pattern across the local subnet
6. **Incident response planning** — produced containment, eradication, recovery, and post-incident recommendations aligned with NIST IR guidance

## Tools Used

- [Volatility 3](https://github.com/volatilityfoundation/volatility3) — memory forensics framework
- [VirusTotal](https://www.virustotal.com) — hash lookup, static & behavioral (sandbox) analysis
- PowerShell — hashing and log processing

## Disclosure
This was completed as a take-home technical assessment for a SOC Analyst role. The memory sample was provided by the assessing organization for evaluation purposes; no proprietary or client data is included. Shared here with permission, for portfolio purposes only.
