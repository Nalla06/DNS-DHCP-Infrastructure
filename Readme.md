## Windows Server DNS and DHCP Infrastructure Lab

![DHCP Server installation screenshot](/Architecture.png)

#### Project Overview

This project demonstrates practical Windows Server 2025 administration in a simulated company environment called **TechOffice GmbH**.

The project focuses on DNS administration, DHCP configuration, Active Directory integration, PowerShell automation, network troubleshooting, and infrastructure validation. It builds on an existing Windows Server and Active Directory lab and extends it with dedicated DNS and DHCP administration tasks.

The objective is to develop practical skills in configuring, managing, testing, and troubleshooting core Windows network services in an environment similar to that used by IT infrastructure and system administration teams.

#### Objectives

- Understand and administer Windows Server DNS and DHCP services.
- Configure and validate DHCP scopes, address allocation, leases, and client options.
- Investigate DNS zones, resource records, reverse lookups, and Active Directory service records.
- Integrate DHCP client configuration with Active Directory DNS.
- Troubleshoot name resolution, IP addressing, and domain-controller discovery.
- Use PowerShell to inspect configurations, validate services, and automate repetitive administrative tasks.
- Simulate infrastructure incidents and document troubleshooting evidence.
- Maintain professional technical documentation and practical evidence in GitHub.

#### Lab Environment

| Component | Configuration |
|---|---|
| Host operating system | Windows 11 |
| Virtualization platform | VMware Workstation Pro 17.6.2 |
| Server operating system | Windows Server 2025 Datacenter Evaluation |
| Server hostname | `SRV-DC01` |
| Active Directory domain | `ad.techoffice.test` |
| Server IPv4 address | `192.168.10.10` |
| Lab network | `192.168.10.0/24` |
| VMware virtual network | VMnet1, host-only |
| Client operating system | Windows 11 |
| Client hostname | `DESKTOP-6NQ8V71` |
| Client IPv4 address after DHCP migration | `192.168.10.100` |

**Lab note:** The host-only network is an isolated practice environment and has no configured default gateway. The addresses and domain names are for this lab, not a production network.

#### Architecture

The existing Windows Server provides Active Directory Domain Services and DNS. Windows DHCP has been installed and configured to allocate client addresses on the lab subnet.

```text
TechOffice GmbH — Virtual Lab
|
|-- Windows Server 2025
|   |-- Hostname: SRV-DC01
|   |-- IPv4: 192.168.10.10
|   |-- Active Directory Domain Services
|   |-- DNS Server
|   |-- DHCP Server
|
|-- VMware VMnet1 — Host-only
|   |-- Network: 192.168.10.0/24
|   |-- DHCP scope: 192.168.10.100–192.168.10.199
|
|-- Windows 11 Client
    |-- Hostname: DESKTOP-6NQ8V71
    |-- IPv4: 192.168.10.100
    |-- DHCP Server: 192.168.10.10
    |-- DNS Server: 192.168.10.10
```

## Project Structure

Detailed documentation is organized into the following sections.

| Part | Area | Main Activities |
|---|---|---|
| 01 | Planning and Initial Audit | Record the existing lab, roles, IP configuration, DNS zones, and baseline checks |
| 02 | DNS Administration | Inspect zones and records, configure reverse lookups, investigate dynamic updates, and validate DNS |
| 03 | DHCP Administration | Install and authorize DHCP, configure scopes and options, and manage leases and reservations |
| 04 | DNS–DHCP Integration | Validate client DNS settings, DNS registration, and Active Directory service discovery |
| 05 | Troubleshooting and Incident Simulations | Investigate DNS timeouts, DHCP problems, failed resolution, and domain-controller discovery |
| 06 | PowerShell Administration and Automation | Build scripts for configuration checks, reporting, and administrative tasks |
| 07 | Testing and Validation | Maintain test cases, record results, and collect screenshots and command output |
| 08 | Final Operations Report | Summarize the architecture, completed work, findings, limitations, and lessons learned |

#### Folder Structure
```text
Windows-Server-DNS-DHCP-Infrastructure/
│
├── README.md
│
├── 01-Planning-and-Audit/
│   └── 01-Initial-Audit.md
│
├── 02-DNS-Administration/
│   ├── 01-DNS-Zones-and-Records.md
│   ├── 02-Reverse-Lookup-Zones.md
│   └── 03-DNS-Validation.md
│
├── 03-DHCP-Administration/
│   ├── 01-DHCP-Installation-and-Authorization.md
│   ├── 02-DHCP-Scope-Configuration.md
│   └── 03-Leases-and-Reservations.md
│
├── 04-DNS-DHCP-Integration/
│   └── 01-Client-Configuration-and-Validation.md
│
├── 05-Troubleshooting-Incidents/
│   └── DNS-Timeout-During-DHCP-Migration.md
│
├── 06-PowerShell-Scripts/
│
├── 07-Testing-and-Validation/
│   └── Validation-Matrix.md
│
├── 08-Final-Operations-Report/
│   └── Final-Report.md
│
├── Screenshots/
│
└── Diagrams/

```
## Practical Skills Demonstrated

### Windows Server Administration
- Windows Server role installation and service validation
- DHCP server authorization and scope management
- DNS administration and Active Directory integration
- Network configuration inspection and troubleshooting

### Networking and Name Resolution
- IPv4 addressing and subnet masks
- DHCP address allocation and lease verification
- DNS A and SRV records
- DNS client configuration
- Active Directory domain-controller discovery

### PowerShell
- Inspecting Windows services, network endpoints, firewall rules, and DNS configuration
- Validating DNS records and network connectivity
- Automating administrative checks and generating reports

### Troubleshooting and Documentation
- Reproducing symptoms and collecting evidence
- Testing hypotheses using read-only commands
- Separating confirmed findings from suspected causes
- Recording incident symptoms, diagnostic steps, root causes, and resolutions

## Learning Outcomes

By completing this project, I aim to demonstrate the ability to configure and administer Windows Server DNS and DHCP services, validate their integration with Active Directory, troubleshoot network-service problems systematically, and document infrastructure work using professional IT administration practices.

## Related Project

This project extends my existing Windows Server and Active Directory lab rather than rebuilding it.

- **Project 01:** Windows Server and Active Directory Administration Lab

