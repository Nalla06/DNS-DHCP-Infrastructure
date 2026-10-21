# Initial Infrastructure Audit

### 1. Project Environment

| Component | Configuration |
|---|---|
| Organisation | TechOffice GmbH |
| Host Operating System | Windows 11 |
| Virtualisation Platform | VMware Workstation Pro 17.6.2 |
| Server Operating System | Windows Server 2025 Datacenter Evaluation |
| Domain Controller | SRV-DC01 |
| Active Directory Domain | ad.techoffice.test |
| Domain Controller IPv4 Address | 192.168.10.10 |
| Subnet | 192.168.10.0/24 |
| Virtual Network | VMware VMnet1 Host-only |
| Client Computer | DESKTOP-6NQ8V71 |

### 2. Initial Findings

##### Active Directory

- The server is an Active Directory domain controller.
- The Windows 11 client is joined to `ad.techoffice.test`.
- Domain controller discovery succeeded using `nltest /dsgetdc:ad.techoffice.test`.

##### DNS

- The DNS Server role is installed and running.
- The forward lookup zones `ad.techoffice.test` and `_msdcs.ad.techoffice.test` are present.
- Both zones are Active Directory-integrated.
- The A record for `SRV-DC01.ad.techoffice.test` resolves to `192.168.10.10`.
- The LDAP domain-controller SRV record resolves successfully.
- A reverse lookup zone for `192.168.10.0/24` was not present during the initial audit. This will be investigated during DNS administration.

##### DHCP

- The Windows DHCP Server role is not installed.
- The Windows 11 client receives its IPv4 configuration from DHCP server `192.168.10.254`.
- VMware VMnet1 has DHCP enabled.
- The reported VMware DHCP range is `192.168.10.128` to `192.168.10.254`.
- The client IPv4 address is `192.168.10.131`.
- The client uses `192.168.10.10` as its DNS server.

##### Network Configuration

- The server uses static IPv4 address `192.168.10.10/24`.
- The server's DNS client setting points to `127.0.0.1`.
- No default gateway is configured on the inspected server and client network interfaces.

### 3. Initial Assessment

The existing Active Directory and DNS configuration is operational based on successful domain-controller discovery and DNS resolution tests.

DHCP is currently provided by VMware rather than Windows Server. Before implementing Windows DHCP, the DHCP scope, exclusions, reservations, DNS options and transition from VMware DHCP must be planned to avoid overlapping DHCP services.

### 4. Planned Work

1. Review existing DNS records and client addressing.
2. Design a Windows DHCP scope for `192.168.10.0/24`.
3. Plan a safe transition from VMware DHCP to Windows DHCP.
4. Configure and validate DNS and DHCP.
5. Perform troubleshooting scenarios and document the results.

## 5. Evidence

Add screenshots showing:

- Windows Server network configuration.
- Installed Windows Server roles.
- DNS zones.
- Successful A and SRV record resolution.
- Windows 11 client IPv4 configuration and DHCP server.
- Successful domain-controller discovery.

**Note:** This report documents the initial observed state. Configuration changes and subsequent test results will be recorded separately.