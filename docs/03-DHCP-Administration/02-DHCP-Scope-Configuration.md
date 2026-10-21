## DHCP Scope Configuration

#### 1. Overview

This document describes the configuration and activation of an IPv4 DHCP scope on `SRV-DC01` for the TechOffice GmbH lab network.

The scope defines the IPv4 addresses that Windows DHCP can lease to clients and supplies the DNS settings required for the lab's Active Directory environment.

#### 2. Lab Environment

| Component | Configuration |
|---|---|
| DHCP server | `SRV-DC01` |
| DHCP server IP | `192.168.10.10` |
| Active Directory domain | `ad.techoffice.test` |
| Virtual network | VMnet1 — Host-only |
| Network address | `192.168.10.0/24` |
| Scope name | `TechOffice-Lab-Clients` |
| Scope range | `192.168.10.100–192.168.10.199` |
| Subnet mask | `255.255.255.0` |
| DNS server option | `192.168.10.10` |
| DNS domain option | `ad.techoffice.test` |
| Default gateway | Not configured — isolated lab |

#### 3. Objectives

- Create an IPv4 DHCP scope.
- Configure the address range and subnet mask.
- Configure DNS client options.
- Avoid competing DHCP services on VMnet1.
- Activate the Windows DHCP scope.
- Validate address allocation from the Windows 11 client.

#### 4. Scope Configuration

##### Step 1 — Open the DHCP console

![DHCP Server installation screenshot](/Images/image-05.png)

1. Sign in to `SRV-DC01`.
2. Open **Server Manager → Tools → DHCP**.
3. Expand the server node.
4. Right-click **IPv4** and select **New Scope**.

##### Step 2 — Configure the scope

Use the following values in the New Scope Wizard:

![DHCP Server installation screenshot](/Images/image-06.png)
| Setting | Value |
|---|---|
| Name | `TechOffice-Lab-Clients` |
| Description | `IPv4 DHCP scope for the TechOffice GmbH lab client network` |
| Start IP address | `192.168.10.100` |
| End IP address | `192.168.10.199` |
| Subnet mask | `255.255.255.0` |
| Exclusions | None configured |
| Lease duration | Wizard default |

![DHCP Server installation screenshot](/Images/image-08.png)

The server's static address, `192.168.10.10`, is outside the dynamic range. Keep infrastructure addresses static or otherwise properly managed so they do not conflict with client leases.

##### Step 3 — Configure scope options

Configure the following options in the wizard:

| DHCP option | Configuration |
|---|---|
| 003 — Router | Left blank |
| 006 — DNS Servers | `192.168.10.10` |
| 015 — DNS Domain Name | `ad.techoffice.test` |

![DHCP Server installation screenshot](/Images/image-10.png)

The gateway option is intentionally blank because VMnet1 is an isolated host-only network without a configured gateway.
![DHCP Server installation screenshot](/Images/image-11.png)

Leave WINS settings blank because they are not required for this lab.

##### Step 4 — Activate the scope

The scope was initially created inactive. To allow Windows DHCP to provide leases, two configuration changes were required.

1. Open VMware's **Virtual Network Editor** on the host.
2. Select **VMnet1**.
3. Disable **Use local DHCP service to distribute IP addresses to VMs**.
4. Apply the VMware network change.
5. Return to the DHCP console on `SRV-DC01`.
6. Right-click the `TechOffice-Lab-Clients` scope and select **Activate** if it is not already active.
![DHCP Server installation screenshot](/Images/image-14.png)

This makes Windows Server DHCP the intended DHCP provider for the isolated lab network rather than VMware's built-in DHCP service.

#### 5. PowerShell Validation

Run these commands in an elevated PowerShell session on `SRV-DC01`.

#### Check the configured scope

```powershell
Get-DhcpServerv4Scope -ComputerName SRV-DC01
```
Expected result: the output includes `192.168.10.0` as the scope network and the range `192.168.10.100` to `192.168.10.199`.

##### Check scope options

```powershell
Get-DhcpServerv4OptionValue `
  -ComputerName SRV-DC01 `
  -ScopeId 192.168.10.0
```
Expected result: the scope-level DNS options include the configured DNS server and DNS domain name. Server-level options can also affect what clients receive, so inspect those if the client results differ.
![DHCP Server installation screenshot](/Images/image-16.png)

### Check the scope state

```powershell
Get-DhcpServerv4Scope -ComputerName SRV-DC01 |
    Format-Table ScopeId, StartRange, EndRange, SubnetMask, State
```
Expected result: the configured scope is shown as `Active`.

## 6. Client Validation

On the Windows 11 client, open Command Prompt and run:

```cmd
ipconfig /all
```
![DHCP Server installation screenshot](/Images/image-15.png)

After the Windows DHCP scope was activated and VMware DHCP was disabled, the client received the following configuration:

| Setting | Observed result |
|---|---|
| IPv4 address | `192.168.10.100` |
| DHCP server | `192.168.10.10` |
| DNS server | `192.168.10.10` |
| DNS suffix | `ad.techoffice.test` |

The client received an address from the intended Windows DHCP scope.

For a fresh lease test, use the following commands only when you are ready to renew the client's network configuration:

```cmd
ipconfig /release
ipconfig /renew
ipconfig /all
```
These commands temporarily disconnect the client from its current IPv4 configuration while the lease is released and renewed. Do not run them during work that depends on the current connection.

## 10. Outcome

The `TechOffice-Lab-Clients` DHCP scope was configured and activated. The Windows 11 client obtained `192.168.10.100` from `SRV-DC01` and received the intended DNS server and domain settings.

