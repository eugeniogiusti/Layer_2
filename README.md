# Layer_2 Notes

# VLAN: Virtual Local Area Network

## Overview

A **VLAN (Virtual Local Area Network)** is a technology that allows for logical segmentation of networks within the same physical infrastructure. It separates network traffic into distinct virtual domains, enabling better performance, security, and flexibility in network management.

---

## Purpose of VLANs

### 1. **Traffic Segmentation**

- Separate network traffic by group or function (e.g., HR, IT, and Finance).
- Prevent devices in different VLANs from communicating directly, reducing congestion.

### 2. **Enhanced Security**

- Isolate sensitive resources in dedicated VLANs to limit access.
- Minimize risks of unauthorized access.

### 3. **Improved Performance**

- Reduce broadcast traffic by confining it to specific VLANs.
- Avoid unnecessary flooding of packets across the entire network.

### 4. **Flexibility in Network Design**

- Reconfigure logical network segmentation without changing physical cabling.
- Easily move devices between VLANs by modifying configurations.

---

## Key Concepts

### Broadcast Domain

A broadcast domain encompasses all devices that receive a broadcast frame sent by one of the devices. VLANs reduce the size of broadcast domains, ensuring that broadcast traffic is limited to devices within the same VLAN.

### Port Modes

#### 1. **Access Port**

- Assigned to a single VLAN.
- Used to connect endpoint devices (e.g., PCs, printers, servers).
- Traffic on access ports is **untagged**, meaning no VLAN information is added to the Ethernet frame.

#### 2. **Trunk Port**

- Carries traffic for multiple VLANs between network devices (e.g., switches, routers).
- Traffic on trunk ports is **tagged** using the **IEEE 802.1Q** standard.

### IEEE 802.1Q Tagging

802.1Q is the standard for VLAN tagging in Ethernet frames. It adds a 4-byte VLAN tag to the Ethernet header:

#### VLAN Tag Structure

- **Tag Protocol Identifier (TPID)**: Indicates the frame is tagged.
- **Priority Code Point (PCP)**: Used for Quality of Service (QoS).
- **VLAN ID (VID)**: Identifies the VLAN to which the frame belongs (range: 1-4094).

Untagged frames are typically assigned to the **native VLAN** (default VLAN for untagged traffic on trunk ports).

---

## Routing Between VLANs

Devices in separate VLANs cannot communicate directly. To enable communication, **inter-VLAN routing** is required. This can be achieved using:

1. **Router-on-a-Stick**

   - A router with a single interface configured as a trunk.
   - Routes traffic between VLANs based on tagging.

2. **Layer 3 Switch**

   - Combines switching and routing capabilities.
   - Routes traffic between VLANs without needing an external router.

---

## Importance of VLANs

- **Security**: Protect sensitive data by isolating critical resources.
- **Efficiency**: Minimize unnecessary broadcast traffic.
- **Scalability**: Adapt to growing and complex networks.
- **Ease of Management**: Simplify logical segmentation without physical changes.


---

## Diagram: Example VLAN Setup

```
+-----------+      Trunk Port       +-----------+
|   Switch  |=======================|   Switch  |
|           |                       |           |
| VLAN 10   |                       | VLAN 10   |
| VLAN 20   |                       | VLAN 20   |
+-----------+                       +-----------+
```
![image](https://github.com/user-attachments/assets/03b3dfce-985c-4252-959f-916b8d852ffa)

![image](https://github.com/user-attachments/assets/7ce4ef11-ed62-49ab-bdd9-1ad3476c5b0d)

- Useful Videos:
- https://www.youtube.com/watch?v=fRuBHSf3Hac

## Basic VLAN Configuration

### Creating VLANs
```cisco
Switch> enable
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name Marketing
Switch(config-vlan)# exit
```

### Assigning Ports to VLANs
```cisco
Switch(config)# interface fastethernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
```

## Interface Configuration

### View Interface Configuration
```cisco
# Show specific interface config
Switch# show running-config interface fastethernet 0/1

# Show all interfaces
Switch# show running-config | section interface

# Show interface status
Switch# show interfaces status
```

### Adding Interface Descriptions
```cisco
Switch(config)# interface fastethernet 0/1
Switch(config-if)# description Marketing
```

## Trunk Configuration

### Firewall Trunk (All VLANs)
```cisco
Switch(config)# interface fastethernet 0/1
Switch(config-if)# description Firewall Trunk
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan all
```

### Server Trunk (Specific VLANs)
```cisco
Switch(config)# interface fastethernet 0/2
Switch(config-if)# description vmware_trunk
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20
```

### Access Points Trunk (IT and Marketing VLANs)
```cisco
Switch(config)# interface fastethernet 0/3
Switch(config-if)# description AP1_Trunk
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 30,40
```

## Security Configuration

### Management VLAN Setup
```cisco
Switch(config)# vlan 30
Switch(config-vlan)# name Management
Switch(config-vlan)# exit
Switch(config)# interface vlan30
Switch(config-if)# ip address 192.168.5.10 255.255.255.0
Switch(config-if)# no shutdown
```

### SSH Configuration
```cisco
Switch(config)# hostname switch_name
Switch(config)# ip domain-name local.net
Switch(config)# crypto key generate rsa
Switch(config)# username admin password your_password
Switch(config)# line vty 0 15
Switch(config-line)# transport input ssh
Switch(config-line)# login local
```

### Console Port Configuration
```cisco
Switch(config)# line console 0
Switch(config-line)# password your_password
Switch(config-line)# login
```

## General Configuration Commands

### View Full Configuration
```cisco
Switch# show running-config
# or
Switch# show run brief
```

### Save Configuration
```cisco
Switch# write memory
# or
Switch# copy running-config startup-config
```
