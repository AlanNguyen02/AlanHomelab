# Homelab Network Setup

## Overview

My homelab uses a separate network behind a TP-Link ER605 router

The Xfinity router provides the main home network, while the ER605 creates a separate LAN for the homelab

## Network Topology

Internet
    |
Xfinity XB8
10.0.0.1
    |
    | 10.0.0.x network
    |
ER605
WAN: 10.0.0.68
LAN: 192.168.0.1
    |
    |
TL-SG108E Switch
    |
    |
Proxmox Server
192.168.0.10

The homelab uses the 192.168.0.0/24 subnet, with the ER605 configured as 192.168.0.1 and the Proxmox server configured with the static IP 192.168.0.10
ER605 uses NAT (Network Address Translation) and routing to connect the home network and the homelab network
Used a port forwarding rule to access the Proxmox web interface from the home Wi-Fi network

## What I learned
IP address identifies devices/interfaces on a network
port identifies a network service
NAT translates network addresses between different networks
Port forwarding directs incoming traffic on a specific port to an internal device
DHCP automatically provides network configuration to devices
Servers can use static IPs to remain predictable

## Troubleshooting
Proxmox was only accessible through direct ethernet connection, but not through Wi-FI

1. Proxmox was running and had IP address 192.168.0.10
2. ER605 WAN connection active
3. ER605 WAN IP was 10.0.0.68
4. Proxmox port forwarding rule was enabled
5. PC was able to now connect to Proxmox via home network.

## Result
The Proxmox interface is accessible from main PC over Wi-Fi using the ER605 WAN address and port forwarding rules.
