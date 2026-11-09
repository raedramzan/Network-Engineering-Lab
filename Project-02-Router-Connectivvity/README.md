# Project 02 – Connecting Two Networks Using a Router

**Platform:** Cisco Packet Tracer  
**Devices:** Cisco 2911 Router, Cisco 2960 Switches, PCs  
**Category:** Network Engineering | Routing | IPv4 Addressing  
**Status:** Completed

## 1. Overview

This project demonstrates how two separate Local Area Networks (LANs) can communicate through a router.

The network consists of two LANs representing Colombo and Kandy. Each LAN has a PC connected to a Cisco Catalyst 2960 switch. Both switches connect to a Cisco 2911 router, which handles communication between the two networks.

The router interfaces are configured with static IPv4 addresses, and each PC uses its local router interface as the default gateway.

The main objective is to establish communication between devices in different IP subnets and understand how a router forwards traffic between networks.

## 2. Network Topology

The topology contains two separate networks connected through one router.

```text
          COLOMBO LAN                    KANDY LAN

          PC_Colombo                    PC_Kandy
        192.168.10.10                 192.168.20.10
              |                             |
              |                             |
        SW1_Colombo                     SW2_Kandy
              |                             |
              |                             |
           G0/0                           G0/1
              \                           /
               \                         /
                +------ R1_Main --------+
                    Cisco 2911 Router
```

### Devices Used

| Device | Model | Quantity |
|---|---|---:|
| Router | Cisco 2911 | 1 |
| Switch | Cisco Catalyst 2960 | 2 |
| End Device | PC-PT | 2 |
| Cable | Copper Straight-Through | 4 |

### Device Connections

| Source Device | Interface | Destination Device |
|---|---|---|
| PC_Colombo | FastEthernet0 | SW1_Colombo |
| SW1_Colombo | Switch FastEthernet port | R1_Main G0/0 |
| R1_Main | G0/1 | SW2_Kandy FastEthernet port |
| SW2_Kandy | FastEthernet port | PC_Kandy FastEthernet0 |

Copper Straight-Through cables are used for the PC-to-switch and switch-to-router connections.

## 3. IP Addressing Plan

Two different IPv4 networks are used for the Colombo and Kandy LANs.

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| PC_Colombo | FastEthernet0 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| R1_Main | GigabitEthernet0/0 | 192.168.10.1 | 255.255.255.0 | — |
| R1_Main | GigabitEthernet0/1 | 192.168.20.1 | 255.255.255.0 | — |
| PC_Kandy | FastEthernet0 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |

### Network Details

**Colombo LAN**
- Network address: `192.168.10.0/24`
- Default gateway: `192.168.10.1`
- PC address: `192.168.10.10`

**Kandy LAN**
- Network address: `192.168.20.0/24`
- Default gateway: `192.168.20.1`
- PC address: `192.168.20.10`

The two LANs belong to different subnets. Therefore, communication between them requires a Layer 3 device such as a router.

## 4. Network Configuration

### 4.1 Build the Topology

Add the following devices to the Cisco Packet Tracer workspace:

- Two PC devices.
- Two Cisco Catalyst 2960 switches.
- One Cisco 2911 router.

Arrange the devices into two separate LANs, with the router positioned between the switches.

Connect the devices using Copper Straight-Through cables.

### 4.2 Configure PC_Colombo

Open PC_Colombo and navigate to:

**Desktop → IP Configuration → Static**

Enter the following values:

```text
IP Address:       192.168.10.10
Subnet Mask:      255.255.255.0
Default Gateway:  192.168.10.1
```

### 4.3 Configure PC_Kandy

Open PC_Kandy and navigate to:

**Desktop → IP Configuration → Static**

Enter:

```text
IP Address:       192.168.20.10
Subnet Mask:      255.255.255.0
Default Gateway:  192.168.20.1
```

### Why is a default gateway required?

A default gateway is used when a device needs to communicate with an IP address outside its local subnet.

For example, PC_Colombo belongs to `192.168.10.0/24`. When it sends data to PC_Kandy at `192.168.20.10`, the destination is outside its local network.

The traffic is therefore forwarded to `192.168.10.1`, which is the router interface connected to the Colombo LAN.

## 5. Router Configuration

The Cisco 2911 router connects both LANs through its GigabitEthernet interfaces.

Open the router CLI and enter privileged EXEC mode:

```bash
enable
configure terminal
```

### 5.1 Configure GigabitEthernet0/0

This interface connects to the Colombo LAN.

```bash
interface gigabitEthernet 0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
exit
```

### 5.2 Configure GigabitEthernet0/1

This interface connects to the Kandy LAN.

```bash
interface gigabitEthernet 0/1
ip address 192.168.20.1 255.255.255.0
no shutdown
exit
```

Exit configuration mode:

```bash
end
```

### Command Explanation

| Command | Purpose |
|---|---|
| `enable` | Enters privileged EXEC mode. |
| `configure terminal` | Enters global configuration mode. |
| `interface gigabitEthernet 0/0` | Selects the router's G0/0 interface. |
| `ip address` | Assigns an IPv4 address and subnet mask. |
| `no shutdown` | Enables the interface. |
| `exit` | Returns to the previous configuration mode. |
| `end` | Exits configuration mode. |

### Why are two interfaces needed?

Each physical router interface connects to a different IP network.

- G0/0 provides access to the Colombo LAN.
- G0/1 provides access to the Kandy LAN.

The router uses these interfaces to receive and forward traffic between the two networks.

## 6. Verify Router Interfaces

After configuration, check the operational status of both interfaces.

```bash
show ip interface brief
```

The output should show the configured addresses:

```text
Interface              IP-Address      Status     Protocol
GigabitEthernet0/0      192.168.10.1    up         up
GigabitEthernet0/1      192.168.20.1    up         up
```

Both interfaces should show `up/up`.

If an interface displays `administratively down`, enter interface configuration mode and use:

```bash
no shutdown
```

## 7. Check the Routing Table

Use the following command:

```bash
show ip route
```

The routing table should contain entries similar to:

```text
C    192.168.10.0/24 is directly connected, GigabitEthernet0/0
C    192.168.20.0/24 is directly connected, GigabitEthernet0/1
```

The letter `C` represents a directly connected route.

These routes are automatically added when the router interfaces are configured with IP addresses and become operational.

**Note:** No static routes or dynamic routing protocols are required in this project because both networks are directly connected to the same router.

## 8. Connectivity Testing

After completing the configuration, connectivity can be checked between the PCs and the router.

### 8.1 Test the Colombo Default Gateway

On PC_Colombo, open:

**Desktop → Command Prompt**

Enter:

```bash
ping 192.168.10.1
```

Successful replies confirm connectivity to the router's Colombo interface.

### 8.2 Test the Kandy Default Gateway

On PC_Kandy, enter:

```bash
ping 192.168.20.1
```

This checks connectivity to the router's Kandy interface.

### 8.3 Test Communication Between the Two LANs

From PC_Colombo:

```bash
ping 192.168.20.10
```

The router receives the packet through G0/0 and forwards it through G0/1 towards PC_Kandy.

PC_Kandy sends its reply back through its default gateway, allowing communication in the opposite direction.

The ping test was successful.

### 8.4 Test in the Opposite Direction

From PC_Kandy:

```bash
ping 192.168.10.10
```

This verifies that communication also works from the Kandy LAN to the Colombo LAN.

Both connectivity tests were successful.

## 9. Troubleshooting

The following checks are useful when communication between the two networks fails.

| Problem | Possible Cause | Solution |
|---|---|---|
| Router interface is down | Interface is disabled | Configure `no shutdown`. |
| PC cannot reach its gateway | Incorrect IP configuration or cable connection | Check the PC address, gateway and physical connection. |
| Remote PC cannot be reached | Incorrect router interface addressing | Verify the IP addresses and subnet masks. |
| Interface is administratively down | Router interface has not been enabled | Enter the interface and use `no shutdown`. |
| Remote network is missing from routing table | Router interface is incorrectly configured or down | Check `show ip interface brief` and `show ip route`. |
| Communication fails in one direction | Incorrect addressing or return-path issue | Check both PCs and router interfaces. |

### Useful Verification Commands

On the router:

```bash
show ip interface brief
show ip route
```

On the PCs:

```bash
ipconfig
ping 192.168.10.1
ping 192.168.20.1
```

These commands help identify whether a connectivity problem is related to IP addressing, router interfaces or the physical network.

## 10. Key Concepts

**Routing:** The process of forwarding packets between different IP networks.

**Default Gateway:** The router interface used by an end device to reach networks outside its local subnet.

**Directly Connected Route:** A route automatically added to the routing table when a router interface is configured and operational.

**Router Interface:** A physical or logical connection between a router and a network.

**ICMP Ping:** A diagnostic test used to check whether another IP device is reachable.

## 11. Skills Practised

- Basic router and switch configuration.
- IPv4 addressing and subnetting.
- Configuring Cisco router interfaces.
- Setting default gateways on end devices.
- Understanding directly connected routes.
- Reading router interface status and routing tables.
- Testing connectivity between different subnets.
- Troubleshooting basic routing problems.

## 12. Project Files and Screenshots

The following structure keeps the Packet Tracer file and supporting evidence organised.

```text
Project-02-Router-Connectivity/
│
├── README.md
├── Project-02-Router-Connectivity.pkt
│
└── screenshots/
    ├── 01-network-topology.png
    ├── 02-colombo-ip-configuration.png
    ├── 03-kandy-ip-configuration.png
    ├── 04-router-interface-configuration.png
    ├── 05-router-interface-status.png
    ├── 06-routing-table.png
    └── 07-successful-connectivity-test.png
```

### Screenshot Evidence

1. **Network topology:** Both LANs, switches, PCs and the router.
2. **Colombo IP configuration:** PC address and default gateway.
3. **Kandy IP configuration:** PC address and default gateway.
4. **Router configuration:** CLI showing both interface configurations.
5. **Interface status:** Output of `show ip interface brief`.
6. **Routing table:** Output of `show ip route`.
7. **Connectivity test:** Successful ping between PC_Colombo and PC_Kandy.

Only screenshots from the actual Packet Tracer simulation should be included.

## Tools Used

- Cisco Packet Tracer
- Cisco 2911 Router
- Cisco Catalyst 2960 Switches
- IPv4
- Ethernet
- Cisco IOS CLI

---

**Project 02 – Completed**  
*Connecting two separate LANs through a Cisco router.*
