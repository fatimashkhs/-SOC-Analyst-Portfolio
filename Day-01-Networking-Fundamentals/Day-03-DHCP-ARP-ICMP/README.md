# Day 3: DHCP, ARP, and ICMP

## Learning Objectives

- Explain DHCP, ARP, and ICMP
- Identify attacks using these protocols
- Investigate SOC alerts involving these protocols

---

## 1. DHCP — Dynamic Host Configuration Protocol

### What It Does
Automatically assigns IP addresses to devices.

### DORA Process
1. Discover — Client asks "Is there a DHCP server?"
2. Offer — Server says "I can give you this IP"
3. Request — Client says "I'll take it"
4. Acknowledge — Server says "Confirmed"

### Ports
- UDP 67 = DHCP Server
- UDP 68 = DHCP Client

### Attacks
- DHCP Starvation: Flood server with fake requests
- Rogue DHCP Server: Attacker gives fake gateway/DNS

### Detection
- Many requests from random MACs
- Unknown DHCP server
- Sudden spike in DHCP traffic

---

## 2. ARP — Address Resolution Protocol

### What It Does
Maps IP addresses to MAC addresses.

### How It Works
- ARP Request: Broadcast "Who has this IP?"
- ARP Reply: Unicast "I have it, here's my MAC"

### ARP Spoofing
Attacker claims to be the gateway. All traffic goes through attacker.
This is a Man-in-the-Middle (MITM) attack.

### Detection
- Same MAC claiming multiple IPs
- ARP replies without requests
- Gateway MAC changes

---

## 3. ICMP — Internet Control Message Protocol

### What It Does
Error messages and network diagnostics.

### Common Uses
- ping (Echo Request/Reply)
- traceroute (Time Exceeded)

### Attacks
- Ping Sweep: Discover live hosts
- ICMP Tunneling: Hide data in ICMP
- ICMP Flood: DoS attack

### Detection
- Many ICMP requests from one source
- Large ICMP packets
- ICMP to external IPs

---

## Investigation Exercise

Alert: ARP spoofing detected
Source MAC: AA:BB:CC:DD:EE:FF
Claimed IP: 192.168.1.1 (gateway)

Answers:
1. Attacker is trying to become the gateway (MITM)
2. ARP spoofing / MITM attack
3. Read/modify traffic, steal credentials
4. Check ARP tables, firewall logs, IDS logs
5. Isolate the offending machine, restore ARP tables

---

## Interview Question

Question: "You see an alert: 'ARP spoofing detected. MAC AA:BB:CC:DD:EE:FF is claiming to be the gateway 192.168.1.1.' How would you investigate this?"

Answer:
1. Identify the MAC address owner
2. Check ARP tables on affected machines
3. Confirm it's not a legitimate change
4. Check for MITM evidence
5. Isolate the machine
6. Restore correct ARP entries
7. Document and escalate

---

## Key Takeaways

1. DHCP = automatic IP assignment (DORA)
2. ARP = IP to MAC mapping
3. ICMP = diagnostics (ping, traceroute)
4. DHCP starvation = flood fake requests
5. ARP spoofing = MITM attack
6. Ping sweep = host discovery
7. ICMP tunneling = data exfiltration
8. All three protocols can be abused by attackers

---

## My Lab

- Gateway MAC: 00:50:56:f7:43:20
- My MAC: 00:0c:29:f1:dd:2b
- DHCP enabled: Yes
