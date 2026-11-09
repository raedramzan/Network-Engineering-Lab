# Project 04 – VLAN Configuration and Trunking

**Platform:** Cisco Packet Tracer  
**Devices:** Cisco Catalyst 2960 Switches, PC-PT  
**Category:** Network Engineering | VLANs | Switching | Trunking  
**Status:** Completed

## 1. Overview

This project focuses on dividing a network into separate VLANs using Cisco Catalyst 2960 switches.

The network represents three departments: HR, Finance and IT. Each department is assigned a separate VLAN to keep its network traffic logically separated.

Two switches are connected through a trunk link, allowing devices in the same VLAN to communicate across both switches.

The configuration also includes a troubleshooting exercise where VLAN 30 is temporarily removed from the allowed VLAN list on the trunk link. This prevents IT devices connected to different switches from communicating until the VLAN is restored.

## 2. Network Topology

The network contains two Cisco 2960 switches and nine PCs.

**Department and VLAN allocation:**

| Department | VLAN ID | Network |
|---|---:|---|
| HR | 10 | 192.168.10.0/24 |
| Finance | 20 | 192.168.20.0/24 |
| IT | 30 | 192.168.30.0/24 |

### Topology Layout

```text
                    SWITCH 1
                 Cisco 2960
              +----------------+
 HR_PC1 ------ | Fa0/1          |
 HR_PC2 ------ | Fa0/2          |
 FIN_PC1 ----- | Fa0/3          |
 FIN_PC2 ----- | Fa0/4          |
 IT_PC1 ------ | Fa0/5          |
 IT_PC2 ------ | Fa0/6          |
              |                |
              | Fa0/24         |
              +---||-----------+
                  ||
             TRUNK LINK
                  ||
              +---||-----------+
              | Fa0/24         |
              |                |
              |     SWITCH 2   |
              |     2960       |
              |                |
 HR_PC3 ------ | Fa0/1          |
 FIN_PC3 ----- | Fa0/2          |
 IT_PC3 ------ | Fa0/3          |
              +----------------+
```

The connection between SW1 and SW2 carries traffic for VLANs 10, 20 and 30.

### Devices Used

| Device | Model | Quantity |
|---|---|---:|
| Switch | Cisco Catalyst 2960 | 2 |
| End Device | PC-PT | 9 |
| Switch-to-switch cable | Copper Crossover | 1 |
| PC-to-switch cables | Copper Straight-Through | 9 |

### Physical Connections

**SW1 connections**

| Device | Switch Port | VLAN |
|---|---|---:|
| HR_PC1 | Fa0/1 | 10 |
| HR_PC2 | Fa0/2 | 10 |
| FIN_PC1 | Fa0/3 | 20 |
| FIN_PC2 | Fa0/4 | 20 |
| IT_PC1 | Fa0/5 | 30 |
| IT_PC2 | Fa0/6 | 30 |

**SW2 connections**

| Device | Switch Port | VLAN |
|---|---|---:|
| HR_PC3 | Fa0/1 | 10 |
| FIN_PC3 | Fa0/2 | 20 |
| IT_PC3 | Fa0/3 | 30 |

**Switch-to-switch connection**

| Switch | Interface | Connected Switch | Interface |
|---|---|---|---|
| SW1 | Fa0/24 | SW2 | Fa0/24 |

A Copper Crossover cable was used for the connection between the two switches in this lab.

## 3. IP Addressing Plan

Each department uses a separate IPv4 subnet corresponding to its VLAN.

| Device | IP Address | Subnet Mask | VLAN |
|---|---|---|---:|
| HR_PC1 | 192.168.10.10 | 255.255.255.0 | 10 |
| HR_PC2 | 192.168.10.11 | 255.255.255.0 | 10 |
| HR_PC3 | 192.168.10.12 | 255.255.255.0 | 10 |
| FIN_PC1 | 192.168.20.10 | 255.255.255.0 | 20 |
| FIN_PC2 | 192.168.20.11 | 255.255.255.0 | 20 |
| FIN_PC3 | 192.168.20.12 | 255.255.255.0 | 20 |
| IT_PC1 | 192.168.30.10 | 255.255.255.0 | 30 |
| IT_PC2 | 192.168.30.11 | 255.255.255.0 | 30 |
| IT_PC3 | 192.168.30.12 | 255.255.255.0 | 30 |

Default gateways are left blank because this project does not include a router or Layer 3 switch.

### Why are separate IP networks used?

Each VLAN represents a separate logical network. Using a different IP subnet for each VLAN makes the department networks easier to identify and manage.

For example:

- HR devices use `192.168.10.0/24`.
- Finance devices use `192.168.20.0/24`.
- IT devices use `192.168.30.0/24`.

A Layer 2 switch does not route traffic between these different networks. Inter-VLAN routing is covered in the next project.

## 4. Switch Configuration

### 4.1 Create VLANs on SW1

Open SW1 and select the CLI tab.

Enter privileged EXEC mode and global configuration mode:

```bash
enable
configure terminal
```

Create the three VLANs:

```bash
vlan 10
name HR
exit

vlan 20
name FINANCE
exit

vlan 30
name IT
exit
```

These commands create the VLANs and assign department names to them.

### 4.2 Assign Access Ports on SW1

Each PC-facing switch port is configured as an access port and assigned to its department's VLAN.

**HR – VLAN 10**

```bash
interface range fastEthernet 0/1-2
switchport mode access
switchport access vlan 10
exit
```

**Finance – VLAN 20**

```bash
interface range fastEthernet 0/3-4
switchport mode access
switchport access vlan 20
exit
```

**IT – VLAN 30**

```bash
interface range fastEthernet 0/5-6
switchport mode access
switchport access vlan 30
exit
```

### What does an access port do?

An access port connects an end device, such as a PC, to a single VLAN.

For example, Fa0/1 and Fa0/2 on SW1 are assigned to VLAN 10, so both HR computers belong to the HR network.

### 4.3 Create VLANs on SW2

The same VLANs must also exist on SW2.

Open SW2 CLI and enter:

```bash
enable
configure terminal
```

Create the VLANs:

```bash
vlan 10
name HR
exit

vlan 20
name FINANCE
exit

vlan 30
name IT
exit
```

VLANs are locally configured on each switch. Creating them on one switch does not automatically create them on the other in this lab.

### 4.4 Assign Access Ports on SW2

Assign the three PCs to their respective VLANs.

**HR – VLAN 10**

```bash
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
exit
```

**Finance – VLAN 20**

```bash
interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
exit
```

**IT – VLAN 30**

```bash
interface fastEthernet 0/3
switchport mode access
switchport access vlan 30
exit
```

## 5. Configure the Trunk Link

A trunk connection is required between SW1 and SW2 because traffic from all three VLANs needs to travel across the same physical connection.

The trunk link uses IEEE 802.1Q VLAN tagging to identify which VLAN an Ethernet frame belongs to.

### 5.1 Configure the Trunk on SW1

On SW1, configure Fa0/24:

```bash
interface fastEthernet 0/24
switchport mode trunk
exit
end
```

### 5.2 Configure the Trunk on SW2

On SW2, configure the corresponding Fa0/24 interface:

```bash
interface fastEthernet 0/24
switchport mode trunk
exit
end
```

**Why must both switch interfaces be configured as trunks?**

Both ends of the connection need to support trunk traffic. This allows frames belonging to multiple VLANs to travel between the switches over a single link.

### Access Port vs Trunk Port

| Access Port | Trunk Port |
|---|---|
| Carries traffic for one assigned VLAN. | Carries traffic for multiple VLANs. |
| Commonly connects to PCs and printers. | Commonly connects switches and routers. |
| Uses an access VLAN assignment. | Uses VLAN tagging to identify traffic. |

## 6. Configure IP Addresses on the PCs

Assign static IP addresses according to the addressing table in Section 3.

For each PC:

1. Open the PC.
2. Select Desktop.
3. Open IP Configuration.
4. Select Static.
5. Enter the correct IP address and subnet mask.
6. Leave the default gateway blank.

For example, HR_PC1 should have:

```text
IP Address:       192.168.10.10
Subnet Mask:      255.255.255.0
Default Gateway:  Leave blank
```

Repeat this process for all nine PCs using their assigned addresses.

## 7. Verify VLAN Configuration

After creating the VLANs and assigning the ports, verify the configuration on both switches.

Run:

```bash
enable
show vlan brief
```

The output should show the VLAN IDs, names and assigned access ports.

**Expected port assignments on SW1:**

| VLAN | Name | Ports |
|---|---|---|
| 10 | HR | Fa0/1, Fa0/2 |
| 20 | FINANCE | Fa0/3, Fa0/4 |
| 30 | IT | Fa0/5, Fa0/6 |

**Expected port assignments on SW2:**

| VLAN | Name | Ports |
|---|---|---|
| 10 | HR | Fa0/1 |
| 20 | FINANCE | Fa0/2 |
| 30 | IT | Fa0/3 |

The trunk ports should not appear as ordinary access ports assigned to these VLANs.

## 8. Verify the Trunk Connection

Check the trunk configuration on both switches:

```bash
show interfaces trunk
```

The output should show Fa0/24 operating as a trunk and carrying VLANs 10, 20 and 30.

This confirms that the switch-to-switch link can transport traffic belonging to all three departments.

## 9. Connectivity Testing

After configuring the VLANs, access ports and trunk link, connectivity can be tested between PCs belonging to the same department.

### 9.1 Test HR Connectivity

From HR_PC1, run:

```bash
ping 192.168.10.12
```

This tests communication between HR_PC1 on SW1 and HR_PC3 on SW2.

A successful response confirms that VLAN 10 traffic can travel across the trunk link.

### 9.2 Test Finance Connectivity

From FIN_PC1, run:

```bash
ping 192.168.20.12
```

This tests communication between the Finance PCs connected to different switches.

### 9.3 Test IT Connectivity

From IT_PC1, run:

```bash
ping 192.168.30.12
```

This tests communication between IT_PC1 on SW1 and IT_PC3 on SW2.

The same-VLAN connectivity tests were successful.

### What do these tests demonstrate?

The tests confirm that:

- Devices within the same VLAN can communicate.
- VLAN traffic can travel between switches through the trunk.
- The switches can maintain separate VLAN networks over one physical connection.

Communication between different VLANs is not available at this stage because no Layer 3 routing has been configured.

## 10. Troubleshooting – Restricting Allowed VLANs

A troubleshooting exercise was performed to see what happens when a VLAN is removed from the trunk's allowed VLAN list.

### 10.1 Introduce the Problem

On SW2, open the CLI and enter:

```bash
enable
configure terminal
interface fastEthernet 0/24
switchport trunk allowed vlan 10,20
end
```

This changes the trunk configuration so that only VLANs 10 and 20 are allowed across the link.

VLAN 30 is now excluded.

### 10.2 Test the Connectivity

Test HR connectivity again:

```bash
ping 192.168.10.12
```

The ping should still succeed because VLAN 10 is allowed across the trunk.

Test Finance connectivity:

```bash
ping 192.168.20.12
```

This should also succeed because VLAN 20 is allowed.

Now test IT connectivity:

```bash
ping 192.168.30.12
```

The ping should fail because VLAN 30 traffic is no longer allowed across the trunk.

### 10.3 Identify the Cause

The trunk link was configured to allow only VLANs 10 and 20.

As a result, frames belonging to VLAN 30 could not pass between SW1 and SW2.

The IT PCs are connected to different switches, so communication between them depends on VLAN 30 being carried across the trunk.

### 10.4 Restore the Configuration

To restore IT connectivity, update the allowed VLAN list:

```bash
enable
configure terminal
interface fastEthernet 0/24
switchport trunk allowed vlan 10,20,30
end
```

Check the trunk again:

```bash
show interfaces trunk
```

Repeat the IT connectivity test:

```bash
ping 192.168.30.12
```

Communication should now be restored.

**Note:** The allowed VLAN list was restored on SW2's trunk interface, where the restriction was introduced.

## 11. Key Concepts

**VLAN (Virtual Local Area Network)**

A VLAN divides a physical switched network into separate logical networks.

**VLAN ID**

A numerical identifier assigned to a VLAN. This project uses VLAN IDs 10, 20 and 30.

**Access Port**

A switch port assigned to one VLAN, normally used to connect an end device.

**Trunk Port**

A switch port that carries traffic belonging to multiple VLANs.

**IEEE 802.1Q**

A standard used to identify VLAN information in Ethernet frames travelling across trunk links.

**VLAN Segmentation**

Separating network devices into different logical networks to improve organisation and limit unnecessary traffic between groups.

**Inter-VLAN Routing**

Routing traffic between different VLANs. This requires a Layer 3 device and is covered in Project 05.

## 12. Skills Practised

- Creating and naming VLANs.
- Assigning switch ports to VLANs.
- Configuring access ports.
- Configuring trunk links between switches.
- Understanding IEEE 802.1Q tagging.
- Verifying VLAN assignments using Cisco IOS commands.
- Checking trunk status and allowed VLANs.
- Testing connectivity across switches.
- Troubleshooting trunk VLAN restrictions.

## 13. Project Files and Screenshots

The project folder is organised as follows:

```text
Project-04-VLANs-and-Trunking/
│
├── README.md
├── Project-04-VLANs-and-Trunking.pkt
│
└── screenshots/
    ├── 01-network-topology.png
    ├── 02-ip-address-configuration.png
    ├── 03-sw1-vlan-configuration.png
    ├── 04-sw2-vlan-configuration.png
    ├── 05-trunk-configuration.png
    ├── 06-show-vlan-brief.png
    ├── 07-show-interfaces-trunk.png
    ├── 08-same-vlan-connectivity.png
    └── 09-trunk-troubleshooting.png
```

### Screenshot Evidence

1. **Network topology:** All nine PCs, both switches and the trunk connection.
2. **IP address configuration:** Example PC configurations from the three departments.
3. **SW1 VLAN configuration:** `show vlan brief` on SW1.
4. **SW2 VLAN configuration:** `show vlan brief` on SW2.
5. **Trunk configuration:** CLI showing `switchport mode trunk`.
6. **VLAN verification:** Output of `show vlan brief`.
7. **Trunk verification:** Output of `show interfaces trunk`.
8. **Connectivity test:** Successful ping between PCs in the same VLAN across two switches.
9. **Troubleshooting:** VLAN 30 connectivity failure while VLAN 10 and VLAN 20 remain operational, followed by the restored configuration if available.

Only screenshots from the actual Packet Tracer simulation should be included.

## Tools Used

- Cisco Packet Tracer
- Cisco Catalyst 2960 Switches
- PC-PT End Devices
- VLANs
- IEEE 802.1Q
- IPv4
- Cisco IOS CLI

---

**Project 04 – Completed**  
*Department-based network segmentation and trunk configuration between two switches.*
