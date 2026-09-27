# Day 2: IP Addressing, Subnetting, and NAT

## Learning Objectives

By the end of this day, I can:
- Explain what a subnet mask is and what it does
- Read CIDR notation and identify network vs host portions
- Calculate usable hosts in a subnet
- Explain what NAT does and why SOC analysts need NAT logs
- Investigate a SOC alert involving public/private IP translation


## 1. Subnet Mask — The Street vs House Rule
A subnet mask is a rule that tells you which part of an IP address is the **network (street)** and which part is the **host (house)**.

**Example:** `255.255.255.0`
IP: 192 . 168 . 1 . 50
Mask: 255 . 255 . 255 . 0

NETWORK (street) HOST (house)

- Street = `192.168.1`
- House = `.50`

**Simple rule:**
- `255` = this part is street (network)
- `0` = this part is house (host)

---

## 2. CIDR Notation
CIDR is a shortcut for writing subnet masks.

| CIDR | Subnet Mask | Meaning |
|------|-------------|---------|
| `/8` | `255.0.0.0` | First 1 part is street |
| `/16` | `255.255.0.0` | First 2 parts are street |
| `/24` | `255.255.255.0` | First 3 parts are street |
| `/25` | `255.255.255.128` | First 3 parts + half of last |
| `/26` | `255.255.255.192` | First 3 parts + quarter of last |

**Example:** `192.168.1.50/24`
- `/24` means first 3 parts are street
- Street = `192.168.1`
- House = `.50`

---

## 3. Binary Basics — Why Powers of 2
A **bit** has only 2 states: `0` or `1`.

Each bit doubles the number of possible values:

| Bits | Values | Power |
|------|--------|-------|
| 1 | 2 | 2^1 |
| 2 | 4 | 2^2 |
| 3 | 8 | 2^3 |
| 4 | 16 | 2^4 |
| 5 | 32 | 2^5 |
| 6 | 64 | 2^6 |
| 7 | 128 | 2^7 |
| 8 | 256 | 2^8 |

An IP address has 4 parts, each 8 bits = 32 bits total.

Each 8-bit part can hold 256 values (0 to 255). That's why each IP part is 0-255.

**Why powers of 2?** Because computers only have 2 states (on/off). Every added bit doubles the possibilities.

---

## 4. Network Address, Broadcast, and Usable Range
For any IP + CIDR:

| Term | How to Find It | Example for `192.168.1.50/24` |
|------|----------------|-------------------------------|
| Network address | Fill house part with `0` | `192.168.1.0` |
| Broadcast address | Fill house part with `255` | `192.168.1.255` |
| Usable range | Everything in between | `192.168.1.1` to `192.168.1.254` |
| Usable hosts | `2^(32-CIDR) - 2` | `2^8 - 2 = 254` |

**Why minus 2?**
- First address = network address (reserved)
- Last address = broadcast address (reserved)

**Usable hosts table:**

| CIDR | Host Bits | Total Values | Usable Hosts |
|------|-----------|--------------|--------------|
| `/24` | 8 | 256 | 254 |
| `/25` | 7 | 128 | 126 |
| `/26` | 6 | 64 | 62 |
| `/27` | 5 | 32 | 30 |
| `/16` | 16 | 65,536 | 65,534 |

## 5. Same Street vs Different Street

| Scenario | What It Means |
|----------|---------------|
| Same street | Direct communication (no router needed) |
| Different street | Traffic goes through a router |

**Example:**
- `10.0.1.5/24` and `10.0.1.100/24` → same street (`10.0.1`)
- `10.0.1.5/24` and `10.0.2.10/24` → different streets (`10.0.1` vs `10.0.2`)

**SOC relevance:** Same subnet = direct. Different subnet = routed. This tells you how an attacker moved.

## 6. NAT — Network Address Translation

### The Problem
- Private IPs (`10.x`, `192.168.x`, `172.16-31.x`) are not routable on the internet
- IPv4 has limited addresses
- Internal machines need to talk to the internet

### The Solution
NAT translates **private IPs → public IPs** when traffic leaves the network.

### Outbound vs Inbound

| Direction | Who Starts It | What NAT Changes |
|-----------|---------------|------------------|
| **Outbound** | Internal machine | Source (private → public) |
| **Inbound** | External machine | Destination (public → private) |

**Outbound example:**
Before NAT: 10.0.5.22:54321 → 185.220.101.5:22
After NAT: 203.0.113.5:54321 → 185.220.101.5:22

**Inbound example:**
Before NAT: 185.220.101.5:22 → 203.0.113.5:54321
After NAT: 185.220.101.5:22 → 10.0.5.22:54321


### Types of NAT
| Type | What It Does |
|------|--------------|
| Static NAT | One private IP ↔ one public IP (fixed) |
| Dynamic NAT | Pool of public IPs, assigned as needed |
| PAT/NAPT | Many private IPs share ONE public IP (most common) |

**PAT** = Port Address Translation. Your home router uses this.

## 7. Why NAT Matters for SOC
**Critical point:** When you see a public IP in logs, you cannot tell which internal machine it was.

**Example alert:** `203.0.113.5 → 45.33.22.11` on port 443
- `203.0.113.5` is your company's public IP
- It could be ANY internal machine
- You need **NAT logs** to find out which one

**NAT log shows:**
Internal IP: 10.0.5.22
Internal Port: 54321
Public IP: 203.0.113.5
Public Port: 54321
Destination: 45.33.22.11:443
Time: 3:45 PM

**Investigation flow:**
Alert with public IP
↓
Check NAT/firewall logs
↓
Find internal IP mapping
↓
Identify the actual machine
↓
Investigate that machine

## 8. My Lab Network
| Device | IP Address | Subnet Mask | Gateway |
|--------|------------|-------------|---------|
| Windows | `192.168.69.135` | `255.255.255.0` | `192.168.69.2` |
| Kali | `192.168.69.x` | `255.255.255.0` | `192.168.69.2` |

**Network address:** `192.168.69.0`
**Broadcast address:** `192.168.69.255`
**Usable range:** `192.168.69.1` to `192.168.69.254`
**Usable hosts:** 254

## 9. Investigation Exercise
### Scenario 1: Internal to Internal
Source IP: 10.0.5.22
Destination IP: 10.0.5.150
Port: 22 (SSH)


**Answers:**
- Same street? Yes (`10.0.5`)
- Router involved? No
- NAT logs? No (both private, internal traffic)

### Scenario 2: Internal to External
Source IP: 10.0.5.22
Destination IP: 185.220.101.5
Port: 22 (SSH)

**Answers:**
- Outbound or inbound? Outbound
- NAT logs? Yes
- What NAT log shows? Internal IP → Public IP mapping
- Next steps: Check NAT logs, identify device, check EDR for process, check threat intel on destination IP

## 10. Interview Question
**Question:** "You see a firewall alert: `203.0.113.5 → 45.33.22.11` on port 443. The firewall blocks it. Your manager asks: 'Which internal machine tried to connect to this suspicious IP?' How would you find out?"
**Answer:**
1. Recognize `203.0.113.5` is the company's public IP (NAT address)
2. Check NAT/firewall logs at the time of the alert
3. Find the internal IP mapping (e.g., `203.0.113.5:54321 → 10.0.5.22:54321`)
4. Identify the device `10.0.5.22`
5. Check EDR/endpoint logs for the process that made the connection
6. Check threat intelligence on `45.33.22.11`
7. Determine if this is a one-time event or recurring
8. Check if other machines are also connecting to this IP

## Key Takeaways

1. Subnet mask = rule for street vs house
2. `/24` = first 3 parts are street
3. Network address = fill host with `0`
4. Broadcast address = fill host with `255`
5. Usable hosts = `2^(32-CIDR) - 2`
6. Same street = direct, different street = routed
7. NAT translates private ↔ public IPs
8. Outbound: NAT changes source
9. Inbound: NAT changes destination
10. NAT logs are essential for SOC attribution
11. Internal IP ≠ safe
12. Public IP in alert = need NAT logs to find internal machine

## Tools Used
- `ipconfig /all` (Windows)
- `ip addr` (Linux/Kali)
- `ip route` (Linux/Kali)

## References
- Cisco Networking Basics
- CompTIA Network+ objectives
- RFC 1918 (Private IP Addresses)
- RFC 3022 (NAT)



