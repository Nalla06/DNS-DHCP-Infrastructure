## DHCP Server Installation and Authorization

#### 1. Overview

This document describes the installation and authorization of the DHCP Server role on the Windows Server 2025 domain controller in the TechOffice GmbH lab.

The objective is to use Windows Server DHCP to assign IP configuration to domain-connected lab clients instead of relying on VMware's built-in DHCP service.

#### 2. Lab Environment

| Component | Configuration |
|---|---|
| Host operating system | Windows 11 |
| Virtualization platform | VMware Workstation Pro 17.6.2 |
| Server operating system | Windows Server 2025 Datacenter Evaluation |
| Server hostname | `SRV-DC01` |
| Active Directory domain | `ad.techoffice.test` |
| Server IPv4 address | `192.168.10.10` |
| Subnet mask | `255.255.255.0` |
| Virtual network | VMnet1 — Host-only |
| DHCP role | Windows Server DHCP |

#### 3. Objectives

- Install the DHCP Server role.
- Complete the post-installation configuration.
- Authorize the DHCP server in Active Directory.
- Verify that the DHCP Server service is running.
- Prepare the server for DHCP scope configuration.

#### 4. Installation and Authorization

##### Step 1 — Install the DHCP Server role

![DHCP service status](/Images/image-03.png)

1. Sign in to `SRV-DC01` using an account with appropriate administrative permissions.
2. Open **Server Manager**.
3. Select **Manage → Add Roles and Features**.
4. Select **Role-based or feature-based installation**.
5. Select `SRV-DC01` as the destination server.
6. Select **DHCP Server** under Server Roles.
7. Accept any required management tools.
8. Complete the wizard and install the role.

##### Step 2 — Complete post-installation configuration

After installation, open the notification flag in Server Manager and select the DHCP post-installation configuration option if it is displayed.

Complete the wizard using an account with the necessary permissions to authorize DHCP in Active Directory.

![DHCP Server installation screenshot](/Images/image-04.png)

##### Step 3 — Verify the DHCP service

Open PowerShell as Administrator and run:

```powershell
Get-Service DHCPServer
```
Expected result: the output identifies the service as `DHCPServer` and shows its status as `Running`.
If the service is not running, investigate the service state before continuing.

##### Step 4 — Verify DHCP authorization

Run:

```powershell
Get-DhcpServerInDC
```
Expected result: the authorized DHCP server is listed with its server name and IP address. In this lab, the expected server is `SRV-DC01` at `192.168.10.10`.

If the server is not listed, check the Active Directory authorization and the account permissions used during post-installation configuration.

## 8. Outcome

The DHCP Server role was installed on `SRV-DC01`, and the server was authorized in Active Directory. This established the foundation for configuring a DHCP scope and providing IP configuration to the Windows 11 lab client.
