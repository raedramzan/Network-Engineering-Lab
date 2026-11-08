# Project 01 – Basic LAN Setup

## Overview

For my first networking lab, I built a basic Local Area Network (LAN) using Cisco Packet Tracer. The aim was to connect two computers through a Cisco Catalyst 2960 switch and test whether they could communicate with each other.

I configured static IP addresses on both PCs, tested the connection using ping, and checked the switch's MAC address table to understand how it learns connected devices.

## Network Topology

The network consists of:

- 2 PCs
- 1 Cisco Catalyst 2960 switch
- 2 Copper Straight-Through cables

### Connection Details

| Device | Interface | Connected To | Interface |
|---|---|---|---|
| PC0 | FastEthernet0 | SW1 | FastEthernet0/1 |
| PC1 | FastEthernet0 | SW1 | FastEthernet0/2 |

## IP Address Configuration

I used the `192.168.10.0/24` network for both computers.

| Device | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| PC0 | 192.168.10.10 | 255.255.255.0 | Not required |
| PC1 | 192.168.10.20 | 255.255.255.0 | Not required |

Both PCs are in the same subnet, so they can communicate through the switch without needing a router.

## Configuration

### 1. Create the topology

I added two PCs and a Cisco 2960 switch to the Packet Tracer workspace.

I connected the devices using Copper Straight-Through cables:

- PC0 FastEthernet0 to SW1 FastEthernet0/1
- PC1 FastEthernet0 to SW1 FastEthernet0/2

### 2. Configure PC0

I opened PC0, went to **Desktop → IP Configuration**, selected Static and entered:

```text
IP Address:      192.168.10.10
Subnet Mask:     255.255.255.0
Default Gateway: Leave blank
```

### 3. Configure PC1

I followed the same steps on PC1 and entered:

```text
IP Address:      192.168.10.20
Subnet Mask:     255.255.255.0
Default Gateway: Leave blank
```

### 4. Check the IP configuration

I used the following command in the PC Command Prompt:

```bash
ipconfig
```

This displays the IP address and subnet mask configured on the computer.

## Testing the Connection

After configuring both PCs, I tested communication in both directions.

From PC0:

```bash
ping 192.168.10.20
```

From PC1:

```bash
ping 192.168.10.10
```

Both computers successfully received replies.

This confirmed that the devices could communicate within the same LAN. The first ping can sometimes time out while the device resolves the destination MAC address, so I repeated the test when necessary.

## Checking the Switch MAC Address Table

I also checked whether the switch had learned the MAC addresses of the connected PCs.

On the SW1 CLI, I entered:

```bash
enable
show mac address-table
```

The command displayed the MAC addresses learned by the switch and their associated interfaces.

For example, PC0's MAC address should be learned on Fa0/1 and PC1's MAC address on Fa0/2.

The actual MAC addresses depend on the devices in Packet Tracer.

## Troubleshooting

I tested what happens when two connected computers are configured in different IP subnets.

I changed PC1's IP address from `192.168.10.20` to `192.168.20.20`, keeping the subnet mask as `255.255.255.0`.

I then tried to ping PC1 from PC0:

```bash
ping 192.168.20.20
```

The ping failed.

The reason was that PC0 belonged to `192.168.10.0/24`, while PC1 belonged to `192.168.20.0/24`. Although both PCs were connected to the same switch, the switch could not route traffic between the two IP networks.

I restored PC1's original IP address (`192.168.10.20`) and tested the connection again. Communication was successful.

## What I Learned

Through this lab, I practised:

- Building a basic LAN in Cisco Packet Tracer.
- Connecting PCs to a Cisco Layer 2 switch.
- Configuring static IPv4 addresses.
- Understanding subnet masks and local network communication.
- Using ping to test connectivity.
- Checking MAC address learning on a switch.
- Troubleshooting an IP addressing problem.

## Files

```text
Project-01-Basic-LAN/
│
├── README.md
├── Project-01-Basic-LAN.pkt
│
└── screenshots/
    ├── network-topology.png
    ├── ip-configuration.png
    ├── successful-ping.png
    └── mac-address-table.png
```

## Tools Used

- Cisco Packet Tracer
- Cisco Catalyst 2960
- IPv4
- Ethernet
- Cisco IOS CLI
