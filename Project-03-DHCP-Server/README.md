# Project 03 – DHCP Server Configuration

**Platform:** Cisco Packet Tracer  
**Devices:** Server-PT, Cisco Catalyst 2960 Switch, PCs  
**Category:** Network Engineering | DHCP | IPv4 Addressing  
**Status:** Completed

## 1. Overview

This project demonstrates how a DHCP server can automatically assign IP configuration details to computers connected to a local network.

A Cisco Packet Tracer server was configured as a DHCP server and connected to a Cisco Catalyst 2960 switch. Three client PCs were connected to the same switch and configured to obtain their network settings automatically.

The DHCP pool was configured with an IP address range, subnet mask, default gateway and DNS server address.

A troubleshooting exercise was also performed by disabling the DHCP service. One client failed to obtain an IP address and received an automatic `169.254.x.x` address instead. The issue was resolved by re-enabling the DHCP service.

## 2. Network Topology

The network contains one DHCP server, one switch and three client computers.

```text
                 DHCP_SERVER
                 192.168.10.2
                      |
                      |
                 Fa0/1 |
                +-----------+
                |   SW1     |
                |  2960     |
                +-----------+
                  |   |   |
                  |   |   |
                Fa0/2 Fa0/3 Fa0/4
                  |   |   |
                 PC1 PC2 PC3
```

All devices are connected to the same LAN through the switch.

### Devices Used

| Device | Model | Quantity |
|---|---|---:|
| Server | Server-PT | 1 |
| Switch | Cisco Catalyst 2960 | 1 |
| Client | PC-PT | 3 |
| Cable | Copper Straight-Through | 4 |

### Physical Connections

| Source Device | Interface | Destination Device | Interface |
|---|---|---|---|
| DHCP_SERVER | FastEthernet0 | SW1 | FastEthernet0/1 |
| CLIENT_01 | FastEthernet0 | SW1 | FastEthernet0/2 |
| CLIENT_02 | FastEthernet0 | SW1 | FastEthernet0/3 |
| CLIENT_03 | FastEthernet0 | SW1 | FastEthernet0/4 |

Copper Straight-Through cables are used for all four connections.

## 3. IP Addressing Plan

The network uses the `192.168.10.0/24` subnet.

The DHCP server has a manually configured static IP address, while the client PCs receive their addresses automatically.

| Device | IP Address | Subnet Mask | Addressing Method |
|---|---|---|---|
| DHCP_SERVER | 192.168.10.2 | 255.255.255.0 | Static |
| CLIENT_01 | 192.168.10.100 | 255.255.255.0 | DHCP |
| CLIENT_02 | 192.168.10.101 | 255.255.255.0 | DHCP |
| CLIENT_03 | 192.168.10.102 | 255.255.255.0 | DHCP |

The three client addresses shown above represent the expected initial allocations when the clients request addresses in this order. Actual allocations can vary depending on the order of DHCP requests.

### DHCP Pool Details

| Setting | Configuration |
|---|---|
| Pool Name | OFFICE_LAN |
| Default Gateway | 192.168.10.1 |
| DNS Server | 192.168.10.2 |
| Start IP Address | 192.168.10.100 |
| Subnet Mask | 255.255.255.0 |
| Maximum Users | 50 |
| DHCP Service | ON |

**Note:** The pool includes `192.168.10.1` as the default gateway, but this particular lab does not contain a router. The gateway value is distributed as a DHCP option; it does not provide internet or access to other networks in this topology.

The DNS address is also distributed as a DHCP option. The DNS service itself would need to be configured separately to provide name resolution.

## 4. Network Configuration

### 4.1 Create the Topology

Open Cisco Packet Tracer and add the following devices:

- One Server-PT device.
- One Cisco Catalyst 2960 switch.
- Three PC-PT devices.

Arrange the server and PCs around the switch.

Connect the devices using Copper Straight-Through cables according to the connection table above.

The server and all three clients should be connected to the same switch.

### 4.2 Configure the DHCP Server's Static IP Address

Before configuring DHCP, the server needs a fixed IP address so that its address does not depend on the DHCP service it provides.

Open the server and navigate to:

**Desktop → IP Configuration**

Select **Static** and enter:

```text
IP Address:       192.168.10.2
Subnet Mask:      255.255.255.0
Default Gateway:  192.168.10.1
DNS Server:       192.168.10.2
```

The server now has a static address within the `192.168.10.0/24` network.

### 4.3 Configure the DHCP Service

Open the server and navigate to:

**Services → DHCP**

Make sure the DHCP service is switched **ON**.

Create a new DHCP pool with the following settings:

```text
Pool Name:         OFFICE_LAN
Default Gateway:   192.168.10.1
DNS Server:        192.168.10.2
Start IP Address:  192.168.10.100
Subnet Mask:       255.255.255.0
Maximum Users:     50
```

Save or add the pool using the button provided in the Packet Tracer DHCP service window.

The DHCP server is now configured to allocate addresses from the `192.168.10.100` range onwards, up to the configured user limit.

### What does each DHCP setting do?

| Setting | Purpose |
|---|---|
| Pool Name | Identifies the DHCP address pool. |
| Default Gateway | Provides the gateway address to DHCP clients. |
| DNS Server | Provides the DNS server address to clients. |
| Start IP Address | Sets the first address available for automatic allocation. |
| Subnet Mask | Defines the network and host portions of the address. |
| Maximum Users | Limits the number of addresses available in the pool. |

## 5. Configure the Client PCs

The client computers must be configured to request their IP addresses automatically.

### CLIENT_01

1. Open CLIENT_01.
2. Select the Desktop tab.
3. Open IP Configuration.
4. Select DHCP.

The client should receive an IP address from the DHCP server.

### CLIENT_02

Repeat the same process:

1. Open CLIENT_02.
2. Navigate to Desktop → IP Configuration.
3. Select DHCP.

### CLIENT_03

Follow the same steps:

1. Open CLIENT_03.
2. Navigate to Desktop → IP Configuration.
3. Select DHCP.

All three PCs should now receive their network settings automatically.

## 6. Verify DHCP Address Allocation

After configuring the clients, check the assigned addresses.

Open each PC and navigate to:

**Desktop → Command Prompt**

Enter:

```bash
ipconfig
```

The output should show an IP address within the configured DHCP range.

Example initial allocations:

| Client | Expected IP Address |
|---|---|
| CLIENT_01 | 192.168.10.100 |
| CLIENT_02 | 192.168.10.101 |
| CLIENT_03 | 192.168.10.102 |

The subnet mask should be `255.255.255.0`.

The default gateway should be `192.168.10.1`, and the DNS server should be `192.168.10.2`.

The exact allocation order can vary depending on when each client requests an address.

## 7. Test Network Connectivity

After receiving their addresses, the client PCs can be tested to confirm communication within the LAN.

### Test 1 – Client to DHCP Server

From CLIENT_01, open Command Prompt and enter:

```bash
ping 192.168.10.2
```

A successful response confirms that the client can communicate with the DHCP server.

### Test 2 – Client to Client

From CLIENT_01, ping the address assigned to CLIENT_02.

For example:

```bash
ping 192.168.10.101
```

Repeat the test between other clients as required.

Successful responses confirm communication between devices within the same LAN.

## 8. Troubleshooting – DHCP Service Failure

A troubleshooting exercise was performed to understand what happens when a client cannot contact its DHCP server.

### 8.1 Introduce the Problem

On the DHCP server:

1. Open the server.
2. Navigate to Services → DHCP.
3. Change the DHCP service from ON to OFF.

The DHCP server will no longer respond to new DHCP requests.

### 8.2 Check CLIENT_03

CLIENT_03 was unable to obtain an IP address from the DHCP server.

Instead, it received an address in the following range:

```text
169.254.x.x
```

This is an Automatic Private IP Addressing (APIPA) address.

### Why did this happen?

When a DHCP client is unable to obtain an address from a DHCP server, it may automatically assign itself a link-local address in the `169.254.0.0/16` range.

This allows limited local communication in some circumstances, but it does not provide a valid address from the configured `192.168.10.0/24` DHCP pool.

CLIENT_01 and CLIENT_02 continued to use their previously assigned addresses because turning off the DHCP service does not immediately remove existing DHCP leases.

### 8.3 Resolve the Problem

To restore DHCP functionality:

1. Open the DHCP server.
2. Navigate to Services → DHCP.
3. Switch the DHCP service back ON.
4. Open CLIENT_03.
5. Navigate to Desktop → IP Configuration.
6. Refresh the DHCP request by switching to Static and then back to DHCP, where necessary.

CLIENT_03 should now receive an address from the configured DHCP pool.

Use the following command to check:

```bash
ipconfig
```

The assigned address should now belong to the `192.168.10.0/24` network.

## 9. Understanding DHCP – DORA Process

DHCP uses a four-step process commonly known as DORA.

| Stage | Name | Description |
|---|---|---|
| 1 | Discover | The client broadcasts a request to locate a DHCP server. |
| 2 | Offer | The DHCP server offers an available IP address. |
| 3 | Request | The client requests the offered address. |
| 4 | Acknowledgement | The server confirms the lease and provides the network configuration. |

This process allows devices to receive their network settings without manually entering an IP address.

### DHCP Ports

- UDP port 67 – DHCP server.
- UDP port 68 – DHCP client.

## 10. Key Concepts

**DHCP (Dynamic Host Configuration Protocol)**

A network protocol that automatically provides IP configuration information to clients.

**DHCP Pool**

A range of IP addresses and related settings available for automatic allocation.

**Static IP Address**

An address manually assigned to a device. It is used for the DHCP server in this project.

**DHCP Lease**

The period during which a client is allowed to use an assigned IP address.

**APIPA**

Automatic Private IP Addressing assigns a link-local address in the `169.254.0.0/16` range when a device cannot obtain an IPv4 address through DHCP and falls back to automatic addressing.

**DNS Server**

A service that translates domain names into IP addresses. In this lab, its address is distributed through DHCP, but DNS service configuration is not part of the project.

## 11. Skills Practised

- Configuring a DHCP server in Cisco Packet Tracer.
- Creating and managing DHCP address pools.
- Assigning static IP addresses to network servers.
- Configuring client PCs to obtain addresses automatically.
- Understanding the DHCP DORA process.
- Verifying IP address allocation.
- Testing communication within a LAN.
- Identifying APIPA addresses.
- Troubleshooting DHCP service failures.

## 12. Project Files and Screenshots

The project folder is organised as follows:

```text
Project-03-DHCP-Server/
│
├── README.md
├── Project-03-DHCP-Server.pkt
│
└── screenshots/
    ├── 01-network-topology.png
    ├── 02-server-ip-configuration.png
    ├── 03-dhcp-pool-configuration.png
    ├── 04-client-01-dhcp-address.png
    ├── 05-client-02-dhcp-address.png
    ├── 06-client-03-dhcp-address.png
    ├── 07-successful-ping-test.png
    └── 08-dhcp-troubleshooting.png
```

## Tools Used

- Cisco Packet Tracer
- Server-PT
- Cisco Catalyst 2960 Switch
- PC-PT End Devices
- DHCP
- IPv4
- ICMP
- Cisco Packet Tracer Services Configuration

---

**Project 03 – Completed**  
*Centralised IP address allocation using DHCP.*
