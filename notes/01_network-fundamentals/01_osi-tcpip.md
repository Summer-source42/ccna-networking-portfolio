# OSI & TCP/IP Models

## Summary (in my words)
- The **OSI model (7 layers)** is a conceptual framework to understand how networks work.
- The **TCP/IP model (4 layers)** is the practical model used on real networks and the internet.
- Thinking in layers helps with **troubleshooting** (start at the lowest layer likely causing the issue).

---

## OSI model (7 layers)
Mnemonic: **All People Seem To Need Data Processing**

1. **Physical (L1)**
2. **Data Link (L2)**
3. **Network (L3)**
4. **Transport (L4)**
5. **Session (L5)**
6. **Presentation (L6)**
7. **Application (L7)**

---

## TCP/IP suite (4 layers)
1. **Physical**
2. **Internet**
3. **Transport**
4. **Application**

---

## OSI ↔ TCP/IP mapping
| TCP/IP Layer | OSI Layers |
|---|---|
| Application Layer | Application Layer (L7), Presentation Layer (L6), Session Layer (L5) |
| Transport Layer | Transport Layer (L4) |
| Network Layer | Network Layer (L3) |
| Physical Layer  | Data Link Layer (L2)  |  Physical Layer (L1) |

---

## What each layer does (quick)
### OSI L7–L5 (Top layers)
- **Application (L7):** user-facing network services and application protocols (e.g., web, email).
- **Presentation (L6):** data formatting/translation, encryption, compression.
- **Session (L5):** manages sessions between applications (start/maintain/terminate).

### OSI L4 (Transport)
- Breaks data into segments, manages **end-to-end delivery** between hosts.
- Handles reliability/ordering/flow control (TCP) or fast best-effort delivery (UDP).
- Uses **port numbers**.

### OSI L3 (Network)
- **Logical addressing + routing** between networks.
- Path selection, forwarding decisions.
- Uses **IP addresses** and **routers**.

### OSI L2 (Data Link)
- **Node-to-node delivery** on the same local network.
- Framing, MAC addressing, switching.
- Uses **MAC addresses** and **switches**.

### OSI L1 (Physical)
- The actual signal transmission over a medium.
- Cables, connectors, radio, fiber; bits on the wire.

---

## Encapsulation & PDUs (how data is packaged)
As data moves down the stack, each layer adds its own header (and sometimes a trailer).

| Layer | PDU name |
|---|---|
| L7–L5 | Data |
| L4 | Segment |
| L3 | Packet |
| L2 | Frame |
| L1 | Bits / Signals |

**Encapsulation flow:**  
Data → Segment → Packet → Frame → Bits

---

## “Who talks to who?” (communication scope)
- **Application:** process-to-process (applications communicating)
- **Transport:** host-to-host (end-to-end between devices)
- **Internet (L3):** end-to-end routing across multiple networks
- **Link (L2):** hop-to-hop / node-to-node on each local link

Example idea:  
Host A → Router → Router → Host B  
- L2 changes each hop (different frames/mediums)  
- L3 stays focused on IP delivery end-to-end  
- L4 ensures host-to-host communication (ports, reliability)

---

## Addressing by layer (important)
- **L2:** MAC address (local delivery)
- **L3:** IP address (network-to-network delivery)
- **L4:** Port numbers (application/service on the host)

---

## Verification (commands)
```text
# Interface / link checks
show interfaces status
show interfaces
show ip interface brief

# L2 checks
show mac address-table
show arp
show vlan brief
show interfaces trunk

# L3 checks
show ip route
ping
traceroute

# (If available on device)
show tcp brief all
show udp
