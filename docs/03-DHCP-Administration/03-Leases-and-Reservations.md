# DHCP Leases and Reservations

####  1. Overview

This document covers DHCP address leases, lease verification, and reservations in the TechOffice GmbH lab.

A DHCP lease assigns an IP address to a client for a defined period. A DHCP reservation allows a specific client, identified by its DHCP client identifier or MAC address as appropriate, to receive a designated address through DHCP.

The initial lease allocation has been verified. Reservation creation and additional lease-management exercises remain to be completed and validated.

####  2. Lab Environment

| Component | Configuration |
|---|---|
| DHCP server | `SRV-DC01` |
| DHCP server IP | `192.168.10.10` |
| DHCP scope | `TechOffice-Lab-Clients` |
| Scope range | `192.168.10.100–192.168.10.199` |
| Client hostname | `DESKTOP-6NQ8V71` |
| Observed client address | `192.168.10.100` |
| DNS server | `192.168.10.10` |
| Active Directory domain | `ad.techoffice.test` |

####  3. Objectives

- Inspect active DHCP leases.
- Identify the client receiving a lease.
- Verify the assigned address and DHCP server.
- Understand the purpose of DHCP reservations.
- Create and validate a reservation in a later practical exercise.
- Troubleshoot common lease and reservation problems.

#### 4. Inspecting DHCP Leases

#####  Step 1 — Open Address Leases

On `SRV-DC01`:

1. Open **Server Manager → Tools → DHCP**.
2. Expand `SRV-DC01`.
3. Expand **IPv4**.
4. Expand `TechOffice-Lab-Clients`.
5. Select **Address Leases**.

Locate the entry for `DESKTOP-6NQ8V71` or the client associated with the observed address `192.168.10.100`.

The lease list displays available client information, which may include the leased IP address, client identifier, hostname, lease expiration, and lease state.

#####  Step 2 — Verify the lease from the client

On the Windows 11 client, run:

```cmd
ipconfig /all
```
Check the following fields:

- IPv4 Address
- DHCP Server
- Lease Obtained
- Lease Expires
- DNS Servers

![DHCP Server installation screenshot](/Images/image-16.png)

The recorded initial result was:

| Field | Observed value |
|---|---|
| IPv4 address | `192.168.10.100` |
| DHCP server | `192.168.10.10` |
| DNS server | `192.168.10.10` |

#####  Step 3 — Inspect leases with PowerShell

On `SRV-DC01`, run:

```powershell
Get-DhcpServerv4Lease `
  -ComputerName SRV-DC01 `
  -ScopeId 192.168.10.0
```
To display selected fields in a table:

```powershell
Get-DhcpServerv4Lease `
  -ComputerName SRV-DC01 `
  -ScopeId 192.168.10.0 |
    Format-Table IPAddress, HostName, ClientId, AddressState, LeaseExpiryTime
```
![DHCP Server installation screenshot](/Images/image-17.png)

The results depend on the current lease state. Use the actual output to document the client identifier and expiration time rather than assuming values.

### 5. Understanding DHCP Reservations

A reservation associates a designated IPv4 address with a particular DHCP client identifier. When that client requests an address, the DHCP server can offer the reserved address.

Reservations are useful when a device needs a predictable IP address but should continue obtaining its network configuration through DHCP.

Examples include selected printers, infrastructure appliances, or lab devices that need a consistent address.

A reservation is not the same as a static IP configured manually on a client. A manually configured static address is not managed as a DHCP lease in the same way, and it must not conflict with addresses managed by DHCP.

## 6. Practical Exercise — Create a Reservation

**Status: Planned — complete this exercise before marking reservation management as finished.**

### Step 1 — Identify the client

On the Windows 11 client, run:

```cmd
ipconfig /all
```

Record the physical network adapter's **Physical Address**. Confirm that it belongs to the adapter connected to VMnet1.

Do not copy a MAC address from a different adapter, such as a disconnected Wi-Fi adapter.

### Step 2 — Choose a reservation address

Choose an address within the configured DHCP scope that is not already assigned to another device or reserved for another client.

Before creating the reservation, inspect current leases and reservations to avoid conflicts. For example, you might choose an unused address such as `192.168.10.150` after verifying that it is available.

### Step 3 — Create the reservation in the DHCP console

1. Open the DHCP console on `SRV-DC01`.
2. Navigate to **IPv4 → TechOffice-Lab-Clients → Reservations**.
3. Right-click **Reservations** and select **New Reservation**.
4. Enter a meaningful reservation name.
5. Enter the selected IP address.
6. Enter the client's verified physical address in the format accepted by the wizard.
7. Choose the appropriate supported reservation type.
8. Save the reservation.

Use the client identifier shown by the environment and the DHCP console. Do not invent or reuse a MAC address.

### Step 4 — Validate the reservation

Inspect the reservation in the DHCP console and verify that the reserved IP and client identifier are correct.

Then perform a controlled client renewal if needed:

```cmd
ipconfig /release
ipconfig /renew
ipconfig /all
```

Confirm that the client receives the reserved address and that its DHCP server remains `192.168.10.10`.

A successful reservation configuration alone is not sufficient proof of successful allocation. Record the actual client result.

## 7. Troubleshooting

### Problem: The client does not appear in Address Leases

Check:

1. Whether the client has an active IPv4 configuration.
2. Whether the client is connected to VMnet1.
3. Whether the DHCP scope is active.
4. Whether `ipconfig /all` identifies `192.168.10.10` as the DHCP server.
5. Whether the DHCP console is displaying the correct scope.

A lease may expire or change, so compare the console with the client's current configuration.

### Problem: The client receives an unexpected address

Run:

```cmd
ipconfig /all
```

Check the DHCP Server field and the IPv4 address.

If the address is outside the expected range, verify that another DHCP server is not responding on the virtual network. Also check for stale client configuration and confirm that VMware DHCP remains disabled on VMnet1.

### Problem: A reservation does not work

Verify the following:

- The reservation is under the correct DHCP scope.
- The reserved IP is valid for the subnet.
- The client identifier matches the intended client.
- The reservation is not conflicting with another configuration.
- The client is communicating with `SRV-DC01`.
- The client has renewed its lease after the reservation was created.

Do not delete leases or reservations as the first troubleshooting action. Gather the current configuration and identify the cause first.

### Problem: The lease is valid, but domain name resolution fails

A valid DHCP lease does not prove that DNS is functioning correctly.

Check the client's DNS settings:

```cmd
ipconfig /all
```

Then test the domain controller's DNS record:

```powershell
Resolve-DnsName SRV-DC01.ad.techoffice.test `
  -Server 192.168.10.10 -Type A
```

Expected result: the query returns `192.168.10.10`.

If DNS resolution fails, investigate DNS separately rather than assuming that the DHCP lease is the cause. The detailed DNS timeout investigation is documented in the project's incident report.

## 8. Validation Checklist

- [x] Client received a DHCP lease.
- [x] Client IPv4 address `192.168.10.100` was observed.
- [x] DHCP server `192.168.10.10` was observed.
- [x] Client lease was located in the DHCP console.
- [ ] Client identifier and lease expiration recorded.
- [ ] Reservation created for a verified client identifier.
- [ ] Reservation allocation validated from the client.
- [ ] Reservation troubleshooting evidence recorded.

## 10. Outcome

The initial DHCP lease allocation was verified: the Windows 11 client received `192.168.10.100` from `SRV-DC01`.
