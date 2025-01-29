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
Switch(config)# vlan 80
Switch(config-vlan)# name Marketing
Switch(config-vlan)# exit
```

### Assigning Ports to VLANs
```cisco
Switch(config)# interface fastethernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 80
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
Switch(config-if)# switchport trunk allowed vlan 90,99
```

### Access Points Trunk (IT and Marketing VLANs)
```cisco
Switch(config)# interface fastethernet 0/3
Switch(config-if)# description AP1_Trunk
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 90,99
```

## Security Configuration

### Management VLAN Setup
```cisco
Switch(config)# vlan 30
Switch(config-vlan)# name Management
Switch(config-vlan)# exit
Switch(config)# interface vlan80
Switch(config-if)# ip address 192.168.80.10 255.255.255.0
Switch(config-if)# no shutdown
```

### Add a vlan on an interface configured in trunk
```
switchport trunk allowed vlan add 80
```


### Show the trunk on the interface
```
show interfaces FastEthernet0/1 trunk
```


### Reset an interface of its configurations
```cisco
default interface FastEthernet0/1
```

## General Configuration Commands

### View Full Configuration
```cisco
Switch# show running-config
# or
Switch# show run brief
```
### show active trunk
```cisco
Switch# show interfaces trunk
```
### View all vlan configured on interfaces

```cisco
Switch# show vlan brief
```




### Save Configuration
```cisco
Switch# write memory
# or
Switch# copy running-config startup-config
```


# Spanning Tree and PortFast Configuration Documentation for Cisco Switches

This documentation explains how to configure and manage **Spanning Tree Protocol (STP)** and **PortFast** on Cisco switches. It covers various network scenarios, necessary configurations, and commands for managing these features.

## Table of Contents

1. [Introduction to Spanning Tree](#introduction-to-spanning-tree)
2. [Spanning Tree Configuration](#spanning-tree-configuration)
3. [PortFast](#portfast)
4. [Verification and Disabling Commands](#verification-and-disabling-commands)
5. [Network Scenarios](#network-scenarios)
6. [Conclusion](#conclusion)

## Introduction to Spanning Tree

![image](https://github.com/user-attachments/assets/a1396a39-e82d-4fbf-9538-a31d04171be5)

![image](https://github.com/user-attachments/assets/c9074de7-5195-498a-99ea-5515ed90a01a)

- Useful video:
- https://www.youtube.com/watch?v=6MW5P6Ci7lw

The **Spanning Tree Protocol (STP)** is used to prevent network loops in a topology with multiple switches. When multiple switches are connected, without STP control, packets could get trapped in an infinite cycle, creating a loop.

### STP Types

There are several STP modes, including:

- **PVST (Per VLAN Spanning Tree)**: Each VLAN has its own spanning tree. This is useful for better managing networks with multiple VLANs.
- **Rapid PVST (R-PVST)**: A faster version of PVST that reduces convergence times.
- **MST (Multiple Spanning Tree)**: Allows multiple STP instances for groups of VLANs.

## Spanning Tree Configuration

To configure **PVST mode** (which is useful for networks with multiple VLANs), use the following command:

```bash
spanning-tree mode pvst
```

### Useful Commands:

- **Check STP mode**:
  ```bash
  show spanning-tree summary
  ```

- **Disable STP (not recommended in production)**:
  ```bash
  conf t
  no spanning-tree mode pvst
  end
  ```

## PortFast

The **PortFast** command is used to speed up the activation process for **access** ports (connected to end devices like PCs, printers, etc.). Without PortFast, a port may stay in the **Listening** and **Learning** state for 30 seconds before transitioning to **Forwarding**, causing delays in device connectivity.

### Command to enable PortFast:

```bash
spanning-tree portfast
```

- **Verify PortFast on an interface**:
  ```bash
  show running-config interface FastEthernet0/1
  ```

- **Disable PortFast on an interface**:
  ```bash
  conf t
  interface FastEthernet0/1
   no spanning-tree portfast
  end
  ```

## Verification and Disabling Commands

Here are the commands to **verify** and **disable** the configuration of Spanning Tree and PortFast.

### Verify Spanning Tree

- **Display Spanning Tree status**:
  ```bash
  show spanning-tree summary
  ```

- **Display Spanning Tree status on an interface**:
  ```bash
  show spanning-tree interface FastEthernet0/1
  ```

### Verify PortFast

- **Verify PortFast on an interface**:
  ```bash
  show running-config interface FastEthernet0/1
  show spanning-tree interface FastEthernet0/1
  ```

### Disable PortFast

To disable PortFast on a port, use:

```bash
conf t
interface FastEthernet0/1
 no spanning-tree portfast
end
```

### Disable Spanning Tree

If you want to disable Spanning Tree (not recommended in a complex network), use:

```bash
conf t
no spanning-tree mode pvst
end
```

## Network Scenarios

### Scenario 1: **Single Switch Network**

In the case of a single switch network, there are no possible loops, but it is still important to configure **Spanning Tree** for future expansion.

- Configure **Spanning Tree** with the command `spanning-tree mode pvst`.
- Enable **PortFast** on **access** ports (connected to PCs and end devices) to reduce connection times.

### Scenario 2: **Multiple Switches Network**

In a network with multiple switches, **Spanning Tree** is essential to prevent loops and ensure a stable network topology.

- Configure **Spanning Tree** with the command `spanning-tree mode pvst`.
- Ensure the **root bridge** is configured properly to manage traffic flow.
- Enable **PortFast** on **access** ports and use **trunking** for the ports connecting switches.

## Conclusion

Configuring **Spanning Tree** and **PortFast** is crucial to ensure a stable and performant network. In your scenario with a single switch, **PVST** is a good choice for managing VLANs in the future, while **PortFast** speeds up the activation process for access ports.

Make sure to **verify** the status of Spanning Tree and PortFast regularly using the appropriate commands to avoid misconfigurations.




# Access Point Configuration Guide for Trunk Mode

## Prerequisites
- Access Point with VLAN support
- Local network access
- Administrative rights to the Access Point

## 1. Static IP Configuration for Management
1. Access the Access Point administration interface
2. Navigate to "Network Settings"
3. Disable DHCP
4. Configure the following parameters:
   - Static IP address: [INSERT_IP]
   - Subnet mask: [INSERT_SUBNET]
   - Default gateway: [INSERT_GATEWAY]
   - Primary DNS: [INSERT_DNS]

![ip](https://github.com/user-attachments/assets/19f3881d-1409-413c-a2f9-ecfbddfb2aca)


## 2. Management VLAN Configuration
1. Navigate to "VLAN Settings"
2. Configure the Management VLAN:
   - Management VLAN ID: [INSERT_VLAN_ID]
   - Ensure this VLAN is tagged on the trunk port
  
![vlan](https://github.com/user-attachments/assets/1491f510-cce8-48d5-acd4-faf6e13ce971)


## 3. VLAN and SSID Configuration
1. For each required wireless network, configure:
   - VLAN ID: [INSERT_VLAN_ID]
   - Corresponding SSID: [INSERT_SSID]
   - Security settings for each SSID:
     - Authentication type
     - Security key/password
     - Encryption (WPA2/WPA3)

### Configuration Example
```
Management VLAN: 99
Guest VLAN: 55 - SSID: Guest_Network
Staff VLAN: 60 - SSID: IT
VoIP VLAN: 100 - SSID: VoIP_Network
```

## 4. Configuration Verification
1. Verify AP is accessible through the management IP
2. Test connectivity for each configured SSID
3. Verify clients connect to the appropriate VLAN

## Important Notes
- Ensure VLANs configured on the AP match the upstream switch configuration
- Document all VLAN IDs and SSIDs for future reference
- Create a backup of the configuration after setup is complete
- Verify the trunk port on the upstream switch is properly configured to allow all necessary VLANs

## Troubleshooting
- If AP is not accessible after static IP configuration:
  - Check physical connectivity
  - Verify configured IP is in the correct subnet
  - Check management VLAN is properly configured on the switch
- If an SSID is not visible:
  - Verify corresponding VLAN is correctly tagged on the trunk
  - Check if SSID is enabled and broadcasting is active

## Best Practices
- Use meaningful SSID names that align with their purpose
- Implement appropriate security measures for each SSID
- Keep detailed documentation of all configurations
- Regularly backup the AP configuration
- Test all SSIDs after configuration changes
- Monitor AP performance and client connectivity

