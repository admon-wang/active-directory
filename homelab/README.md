# Active Directory Homelab: Security Testing & Threat Detection

## Project Overview

This homelab project demonstrates a comprehensive Active Directory environment designed for security research, threat detection, and attack simulation. The lab environment mirrors real-world enterprise infrastructure to enable hands-on learning of cybersecurity concepts, attack methodologies, and defensive detection strategies.

## Objectives

- Build a realistic Active Directory infrastructure in a controlled environment
- Simulate real-world attack scenarios using Kali Linux as an attack platform
- Monitor and log all activity using Splunk for centralized log aggregation
- Generate security telemetry using Sysmon and Splunk Universal Forwarder
- Execute attack simulations with Atomic Red Team to test detection capabilities
- Develop detection rules and security monitoring playbooks

## Lab Architecture

### Network Topology
- Domain: MyDFIR
- Network: 192.168.10.0/24
- Splunk Server: 192.168.10.10
- Active Directory: 192.168.10.7
- Attacker: 192.168.10.250 (Kali Linux)

### Components

#### 1. Active Directory Server (192.168.10.7)
- OS: Windows Server
- Role: Domain Controller
- Tools: Sysmon, Splunk Universal Forwarder
- Purpose: Central identity and access management, event logging
- Logs Generated: Authentication events, privilege escalation, group policy changes

#### 2. Splunk Server (192.168.10.10)
- OS: Linux/Windows
- Role: Centralized log aggregation and analysis platform
- Purpose: Collect, parse, and visualize security events from all endpoints
- Data Ingestion: Receives logs from Sysmon and Universal Forwarder agents

#### 3. Windows 10 Workstation (DHCP)
- OS: Windows 10
- Tools:
  - Splunk Universal Forwarder
  - Sysmon
  - Atomic Red Team
- Purpose: Endpoint testing platform for simulating user workstations under attack

#### 4. Kali Linux Attack Machine (192.168.10.250)
- OS: Kali Linux
- Role: Penetration testing and attack simulation platform
- Tools: Exploitation frameworks, password crackers, post-exploitation utilities
- Purpose: Simulate adversary tactics, techniques, and procedures (TTPs)

## Key Technologies

### Monitoring & Telemetry
- Sysmon: Windows system monitoring tool generating detailed process, file, and network telemetry
- Splunk Universal Forwarder: Lightweight log forwarding agent installed on endpoints
- Splunk Enterprise: Central platform for log aggregation, analysis, and visualization

### Attack Simulation
- Atomic Red Team: Framework executing small, focused tests that map to MITRE ATT&CK tactics
- Kali Linux tools: Exploitation, lateral movement, and post-exploitation utilities

### Logging & Detection
- User authentication events
- Process creation and execution
- Network connections and DNS queries
- File and registry modifications
- Privilege escalation attempts
- Lateral movement activities

## Learning Goals

1. Understand Active Directory attack vectors and defenses
2. Learn to configure monitoring and log aggregation
3. Develop skills in threat detection and incident response
4. Map attack techniques to MITRE ATT&CK framework
5. Create effective detection rules for security tools
6. Practice defensive security operations

## Project Structure

```text
homelab/
├── README.md
├── docs/
├── configs/
├── splunk/
├── detection-rules/
├── attack-scenarios/
├── scripts/
└── lab-diagrams/
```

## Getting Started

- Review the documentation under `docs/` for setup guides for each component
- Use `configs/` for system configuration templates
- Check `attack-scenarios/` for attack simulation walkthroughs
- Use `splunk/` for queries, dashboards, and alert examples

## Security Disclaimer

This lab is designed for educational purposes only in an isolated, controlled network environment. All testing should be performed only on systems you own or have explicit permission to test.

---

**Last Updated:** September 2026
