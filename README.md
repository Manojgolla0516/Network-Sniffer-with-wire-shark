# Network-Sniffer
Network sniffing is the process of intercepting and logging traffic as it passes over a digital network. When using Wireshark, the world’s most widely used network protocol analyzer, this process transitions from simple interception to deep-packet inspection.

# network-sniffer

A hands-on network traffic analysis project using **Wireshark** on Kali Linux. Captures and analyzes real TCP, UDP, ICMP, and DNS traffic with documented findings.

> ⚠️ **Ethical use only.** Only capture traffic on networks you own or have explicit written permission to monitor.

---

## Tools Used

- **Wireshark** — GUI packet capture and analysis tool
- **Kali Linux** — security-focused Linux distribution
- **ping, curl** — tools used to generate test traffic

---

## What I Learned

- How to capture live network traffic on a specific interface
- How to apply Wireshark display filters to isolate specific traffic
- The difference between TCP, UDP, ICMP, and DNS protocols
- How to read packet details — Ethernet → IP → TCP/UDP → Data layers
- How DNS resolves domain names before a connection is made

---

## Wireshark Filters Used

| Filter | What it shows |
|--------|--------------|
| `tcp` | All TCP packets |
| `udp` | All UDP packets |
| `icmp` | Ping packets |
| `udp.port == 53` | DNS lookups |
| `tcp.port == 80` | HTTP traffic |
| `tcp.port == 443` | HTTPS traffic |
| `ip.addr == 10.0.2.15` | All traffic to/from my machine |

---

## Captures

### 1. DNS Lookup (UDP)
**Command used:**
```bash
ping google.com
```
**Filter:** `udp.port == 53`

**What happened:**
Before ping sends a single packet, the OS first sends a DNS query over UDP port 53 to resolve `google.com` to an IP address. This is visible in Wireshark as a UDP packet to `8.8.8.8` (Google DNS).

**Key observation:** DNS always uses UDP, not TCP — it's a fast, lightweight lookup.

---

### 2. ICMP Ping Traffic
**Command used:**
```bash
ping google.com
```
**Filter:** `icmp`

**What happened:**
After DNS resolves the IP, ICMP Echo Request packets are sent to Google's server. Each one gets an ICMP Echo Reply back. Wireshark shows the round-trip time for each packet.

**Key observation:** ping uses ICMP — not TCP or UDP. That's why filtering by `tcp` or `udp` hides ping traffic.

---

### 3. HTTP Traffic (TCP)
**Command used:**
```bash
curl http://example.com
```
**Filter:** `tcp.port == 80`

**What happened:**
A full TCP handshake is visible — SYN → SYN-ACK → ACK — before any data is sent. This is how every TCP connection starts. After the handshake, the HTTP request and response are exchanged.

**Key observation:** TCP always does a 3-way handshake before sending data. UDP does not.

---

## How to Reproduce

1. Install Kali Linux (or any Linux distro with Wireshark)
2. Open Wireshark: `sudo wireshark`
3. Select your network interface (e.g. `eth0`)
4. Start capture
5. Run any of the commands above in a second terminal
6. Apply the filters in the Wireshark filter bar
7. Click the red ■ to stop capture
8. File → Save As → `capture.pcap` to save

---

## Protocol Cheat Sheet

| Protocol | Layer | Port | Used for |
|----------|-------|------|----------|
| ICMP | Network | — | ping, traceroute |
| DNS | Application | UDP 53 | Domain name resolution |
| HTTP | Application | TCP 80 | Web traffic (unencrypted) |
| HTTPS | Application | TCP 443 | Web traffic (encrypted) |
| SSH | Application | TCP 22 | Secure remote access |

---

## Disclaimer

All captures were performed on a personal Kali Linux VM in an isolated VirtualBox environment. No third-party networks were monitored.

---
