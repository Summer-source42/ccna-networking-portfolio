# IP Addressing (IPv4 + IPv6)

## Summary
- **IPv4** is 32-bit addressing. Even though modern networks use **CIDR**, CCNA still expects understanding of **classful ranges**, private ranges, and basic multicast.
- **IPv6** is 128-bit addressing with address types (global/ULA/link-local), multicast scopes, and interface IDs (often via **modified EUI-64**).
- For troubleshooting, I identify: **address type**, **scope**, **prefix length**, and whether the address is **routable**.

---

# IPv4 Addressing

## IPv4 basics
- Address size: **32 bits**
- Total addresses: **2^32 = 4,294,967,296**
- Address conservation techniques commonly used:
  - **VLSM / CIDR**
  - **Private IPv4 + NAT**

---

## IPv4 Classes (classful addressing)
> Modern networks use CIDR, but class ranges are still important for fundamentals/exams.

| Class | First Octet Range | Default Mask | Notes |
|---|---:|---:|---|
| A | 1–126 | /8 (255.0.0.0) | Large networks |
| B | 128–191 | /16 (255.255.0.0) | Medium networks |
| C | 192–223 | /24 (255.255.255.0) | Small networks |
| D | 224–239 | N/A | Multicast |
| E | 240–255 | N/A | Experimental |

**Special IPv4 ranges (quick)**
- `127.0.0.0/8` loopback
- `169.254.0.0/16` APIPA (self-assigned)
- `0.0.0.0` unspecified / “this host”
- `0.0.0.0/0` default route

---

## Private IPv4 ranges (RFC1918)
- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

---

## IPv4 multicast groups (common)
| Purpose | IPv4 Multicast |
|---|---|
| All hosts | `224.0.0.1` |
| All routers | `224.0.0.2` |
| OSPF routers | `224.0.0.5` |
| OSPF DR/BDR | `224.0.0.6` |
| RIP routers | `224.0.0.9` |
| EIGRP routers | `224.0.0.10` |

---

# IPv6 Addressing

## Why IPv6
- Main reason: **IPv4 exhaustion**
- IPv6 address size: **128 bits**
- IPv6 does **not** use broadcast (many functions rely on **multicast** + **NDP**)

---

## IPv6 communication types (routing schemes)
- **Unicast (1 → 1)**: one source to one destination
- **Multicast (1 → many)**: one source to a multicast group
- **Anycast (1 → nearest)**: same unicast address on multiple devices; routing selects the closest

### Anycast notes (from your notes)
- No special “anycast-only” range in IPv6
- Use a **normal unicast address**, configured on multiple routers/interfaces
- Routing delivers to the “nearest” one

Example idea:
```text
ipv6 address 2001:db8:1:1::99/128   (used as anycast)
```
--- 

## Special IPv6 addresses

### Unspecified
- `::` = unspecified IPv6 address
- Used when a device doesn’t know its IPv6 address yet
- IPv6 default route: `::/0` (IPv4 equivalent: `0.0.0.0/0`)

### Loopback
- `::1` = loopback
- Used to test the local protocol stack (IPv4 equivalent: `127.0.0.0/8`)

---

## Link-local addresses

### What they are
- Auto-generated on IPv6-enabled interfaces
- Used within a single link/subnet

### Common uses
- Routing protocol neighbor adjacencies (example: OSPFv3)
- Next-hop for static routes
- Neighbor Discovery Protocol (NDP), which replaces ARP functionality

### Prefix
- Link-local range: `FE80::/10`

### Cisco example


interface g0/0
 ipv6 enable


## Multicast addresses

### Range
- IPv6 multicast range: `FF00::/8`

### Common IPv6 multicast groups (+ IPv4 equivalents)
| Purpose | IPv6 Multicast | IPv4 Multicast |
|---|---|---|
| All nodes | `FF02::1` | `224.0.0.1` |
| All routers | `FF02::2` | `224.0.0.2` |
| All OSPF routers | `FF02::5` | `224.0.0.5` |
| All OSPF DRs/BDRs | `FF02::6` | `224.0.0.6` |
| All RIP routers | `FF02::9` | `224.0.0.9` |
| All EIGRP routers | `FF02::A` | `224.0.0.10` |

- `FF02` = link-local scope → stays inside the local subnet.

### IPv6 multicast scopes
- **Interface-local:** `FF01` (doesn’t leave the local device)
- **Link-local:** `FF02` (stays in local subnet)
- **Site-local:** `FF05` (limited to a site)
- **Organization-local:** `FF08` (wider than site-local)
- **Global:** `FF0E` (can be routed globally)

---

## Global Unicast vs Unique Local (ULA)

### Global Unicast (Internet-routable)
- Routable on the internet
- Commonly associated with: `2000::/3`

### Unique Local Address (ULA) (private IPv6)
- Private addressing (not internet-routable)
- Range: `FC00::/7`
- In practice commonly used: `FD00::/8`
- Used inside internal networks; not routed on the public internet

**ULA structure idea**
- `FD` + **40-bit Global ID (random)** + **16-bit subnet** + **64-bit interface ID**

---

## Hex + IPv6 shortening

### Hex / bases recap
- Binary = base2
- Decimal = base10
- Hexadecimal = base16

### Binary → Hex (quick method)
Group into 4-bit chunks:
- `1101 1011` → `0xDB`
- `0010 1111` → `0x2F`

### IPv6 abbreviation (shortening) rules
1) Remove leading zeros in a hextet  
2) Replace one consecutive run of all-zero hextets with `::` (only once)

Example:
- `2007:0000:0000:0000:20A7:0000:0000:34BD`
- `2007::20A7:0:0:34BD`


## Finding the IPv6 prefix (global unicast subnetting)

### Common enterprise scenario: `/48` → `/64`
- An enterprise may receive a `/48` from an ISP.
- Typical IPv6 LAN subnets use `/64`.
- That leaves **16 bits** (between `/48` and `/64`) for the enterprise to create subnets.

### How to think about it
If you have:
- `2001:db8:0b00::/48`

You can create `/64` subnets by using the **4th hextet** as the subnet ID:
- `2001:db8:0b00:0001::/64`
- `2001:db8:0b00:0002::/64`
- `2001:db8:0b00:0007::/64`

### `/56` prefix example
For an address like:
- `300D:00F2:0B34:2100:0000:0000:1200:0001/56`

The prefix is the first **56 bits**. A clean way to represent the network is:
- `300D:F2:B34:2100::/56`

(Meaning: network identified up to `/56`, host bits follow.)

---

## Modified EUI-64 (MAC → IPv6 Interface ID)

### What EUI-64 is
- Converts a **48-bit MAC** into a **64-bit interface identifier**
- Often used for the host portion of a `/64` IPv6 address

### UAA vs LAA + U/L bit
MAC addresses can be:
- **UAA** (Universally Administered Address): assigned by manufacturer
- **LAA** (Locally Administered Address): assigned by an admin

**U/L bit meaning**
- U/L bit = **0 → UAA**
- U/L bit = **1 → LAA**

In modified EUI-64, you **flip** that bit when generating the interface ID.

### EUI-64 conversion steps
1) Split the MAC into two halves  
2) Insert `FFFE` in the middle  
3) Flip the **U/L bit** (the “7th bit”) of the first byte

### Example 1
MAC:
- `78DB.CBAC.0867`

Insert `FFFE`:
- `78DB.CBFF.FEAC.0867`

Flip U/L bit in first byte:
- `78` → `7A`

Final EUI-64 interface ID:
- `7ADB:CBFF:FEAC:0867`

### Example 2
MAC:
- `00FF.6BA6.F456`

Insert `FFFE`:
- `00FF.6BFF.FEA6.F456`

Flip U/L bit:
- `00` → `02`

Final:
- `02FF:6BFF:FEA6:F456`



### Cisco config (EUI-64)

ipv6 unicast-routing
interface g0/0
 ipv6 address 2001:db8:0:0::/64 eui-64
 no shutdown

### Verification commands
Windows
ipconfig /all
ping <default-gateway>
ping 8.8.8.8
tracert 8.8.8.8
nslookup google.com

###Cisco (IPv6)
show ipv6 interface brief
show ipv6 interface g0/0
show ipv6 neighbors
show ipv6 route
ping ipv6 <address>
traceroute ipv6 <address>

###Fast troubleshooting (quick checks)
Does the interface have a link-local FE80:: address?
Is the address type correct (global vs ULA vs link-local)?
Are you trying to route a link-local address across subnets? (won’t work)
If routing adjacency fails, verify multicast scope + NDP/neighbor behavior.

