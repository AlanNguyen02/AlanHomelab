# Linux Network Troubleshooting

## Objective

Practice diagnosing network connectivity and DNS issues from an Ubuntu Server VM

## Network Configuration

- Ubuntu VM: 192.168.0.103/24
- Default gateway: 192.168.0.1
- DNS server: 192.168.0.1
- ER605 WAN: 10.0.0.68
- Xfinity gateway: 10.0.0.1

## Troubleshooting Process

### 1. Verify Network Interface

Used `ip addr` to verify that `ens18` was up and had an IPv4 address

Result: `192.168.0.103/24`

### 2. Verify Default Route

Used `ip route` to verify the default gateway

Result: `default via 192.168.0.1`

### 3. Test Local Gateway

Used `ping -c 4 192.168.0.1`

Result: 4/4 received, 0% packet loss

### 4. Test Upstream Router

Used `ping -c 4 10.0.0.1`

Result: 4/4 received, 0% packet loss

### 5. Test Internet Connectivity

Used `ping -c 4 8.8.8.8`

Result: 4/4 received, 0% packet loss

### 6. Test DNS Resolution

Used `ping -c 4 google.com` and `resolvectl query google.com`

Result: DNS successfully resolved `google.com` to IPv4 and IPv6 addresses

## What I Learned

- How to verify whether a Linux network interface is operational
- How to identify the default gateway
- How to test connectivity progressively from the local network to the Internet
- The difference between testing an IP address and testing DNS resolution
- How Ubuntu uses the ER605 as its DNS server
- How to isolate networking problems instead of changing settings blindly

## Troubleshooting Method

1. Identify the problem
2. Gather evidence
3. Test the closest network component first
4. Progressively test farther from the system
5. Isolate where connectivity fails
6. Fix the identified problem
7. Verify the result
