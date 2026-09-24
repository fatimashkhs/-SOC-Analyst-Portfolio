# Day 1: Networking Fundamentals

## What is a Network?

A network is a group of connected devices that communicate with each other and exchange data.

For example, computers, phones, servers, routers, and other devices can communicate through a network.

In cybersecurity, understanding networks is important because SOC analysts investigate network traffic, IP addresses, ports, protocols, and suspicious connections.

## Why TCP/IP Exists

TCP/IP is a collection of networking protocols that allows devices to communicate with each other across networks.

TCP stands for Transmission Control Protocol. It provides reliable communication by making sure data is delivered correctly and in the correct order.

IP stands for Internet Protocol. It is responsible for addressing and routing packets between devices.

TCP/IP is important for cybersecurity because SOC analysts need to understand how normal network communication works before they can identify suspicious or malicious traffic.

## Public vs Private IP Addresses

A private IP address is used inside a local network and is not directly reachable from the public Internet.

Common private IPv4 ranges include:

- 10.0.0.0/8
- 172.16.0.0/12
- 192.168.0.0/16

My lab IP address is:

192.168.69.136

This is a private IPv4 address because it belongs to the 192.168.0.0/16 private range.

A public IP address is an address that can be used to communicate over the Internet. It is normally assigned by an Internet Service Provider or another network provider.

## Common Ports

| Port | Protocol | Service | SOC Relevance |
|------|----------|---------|---------------|
| 22   | TCP      | SSH     | Remote administration; investigate unusual login attempts or connections |
| 80   | TCP      | HTTP    | Web traffic; investigate suspicious requests and connections |
| 443  | TCP      | HTTPS   | Encrypted web traffic; common in normal Internet communication |
| 53   | UDP/TCP  | DNS     | Domain name resolution; investigate unusual DNS queries |
| 3389 | TCP      | RDP     | Remote Desktop; monitor for suspicious remote access |
| 445  | TCP      | SMB     | File/printer sharing; monitor for suspicious lateral movement |

## My Lab IP Addresses

- My IP: 192.168.69.136
- My Gateway: 192.168.69.2

My network interface is:

- Interface: eth0

## Investigation Exercise

1. What is my IPv4 address?

My IPv4 address is 192.168.69.136.

2. What is my default gateway?

My default gateway is 192.168.69.2.

3. What is my network?

My network is 192.168.69.0/24.

4. What is the difference between an IP address and a MAC address?

An IP address is used for logical network addressing and communication between networks. A MAC address identifies a network interface at the data-link layer.

5. What is the difference between IPv4 and IPv6?

IPv4 uses 32-bit addresses, while IPv6 uses 128-bit addresses. IPv4 addresses are commonly written using four decimal numbers separated by dots, while IPv6 addresses use hexadecimal numbers separated by colons.

6. What is a default gateway?

A default gateway is the device/router that a computer sends traffic to when the destination is outside its local network.

7. What is localhost?

127.0.0.1 is the IPv4 loopback address, commonly called localhost. It refers to the local computer itself.

## Key Takeaways

Today I learned the basic concepts needed to understand computer networks.

I learned that an IP address identifies a device/interface on a network and that IPv4 and IPv6 are two different versions of IP addressing.

I learned how to identify my own IPv4 address and default gateway using Linux networking commands.

My lab IPv4 address is 192.168.69.136 and my default gateway is 192.168.69.2.
