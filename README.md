# Cisco Switch Configuration Guide

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
Switch(config-if)# description Proxmox Management
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
Switch(config-if)# description Proxmox Trunk
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20
```

### Access Points Trunk (Staff and Guest VLANs)
```cisco
Switch(config)# interface fastethernet 0/3
Switch(config-if)# description AP1 Trunk
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
Switch(config-if)# ip address 192.168.30.10 255.255.255.0
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

## Notes
- Always save configuration changes using `write memory` or `copy running-config startup-config`
- When configuring SSH, the domain name is required for RSA key generation but can be any valid domain format
- Management VLAN (VLAN 30) is used for secure switch administration
- Trunk ports should be configured based on security requirements:
  - Firewall trunk allows all VLANs
  - Server trunk allows only specific server VLANs
  - AP trunks allow only staff and guest VLANs
