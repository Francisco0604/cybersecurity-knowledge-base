# Wireshark: Network Protocol & Packet Analysis Guide

Wireshark is an industry-standard network protocol analyzer used to capture, inspect, and decode network traffic in real time. It enables security analysts, network engineers, and penetration testers to observe packet flows at microscopic detail across the entire OSI model.

Unlike application-layer web proxies (e.g., Burp Suite) that focus on HTTP/HTTPS requests and responses, Wireshark captures raw traffic across all network layers and protocols (Ethernet, ARP, IP, ICMP, TCP, UDP, DNS, TLS, and Application protocols).

---

## 1. Core Architecture & Operating Principles

### 1.1 How Wireshark Operates

```text
[ Physical / Wireless Network Interface ]
                   │
                   ▼
       [ Packet Capture Driver ] (Npcap / libpcap)
                   │
         [ Capture Filter ] (BPF - filters during recording)
                   │
                   ▼
         [ Wireshark Memory Buffer / PCAP Engine ]
                   │
          [ Display Filter ] (Filters visual representation)
                   │
                   ▼
      [ Protocol Dissectors & Decoders ]
                   │
                   ▼
     [ 3-Pane Graphical Analysis Interface ]
```

1. **Packet Capture Driver:** Interfaces with the Network Interface Card (NIC) in promiscuous/monitor mode via Npcap (Windows) or libpcap (Linux).
2. **Protocol Dissectors:** Modular decoders that parse raw byte streams into protocol-specific fields (e.g., TCP flags, DNS questions, TLS records).
3. **Three-Pane GUI Layout:**
   - **Packet List Pane:** Chronological list of packets (No., Time, Source, Destination, Protocol, Length, Info).
   - **Packet Details Pane:** Hierarchical breakdown of protocols for the selected packet.
   - **Packet Bytes Pane:** Raw hex and ASCII dump of packet payload.

---

## 2. Network Interfaces & Capture Selection

Selecting the correct interface is essential; Wireshark only captures packets traversing the chosen adapter:

| Interface Type | Function / Description | Common Use Case |
| :--- | :--- | :--- |
| **Wi-Fi Adapter** | Captures wireless 802.11 / WLAN network traffic. | Inspecting client internet traffic, local subnet communication. |
| **Ethernet Adapter** | Captures physical wired LAN traffic. | Enterprise network auditing, switch/router analysis. |
| **Loopback Adapter** (`127.0.0.1` / `localhost`) | Captures inter-process communication on the local machine. | Debugging local web servers, API microservices, Docker containers. |
| **VMware Adapters** (`VMnet1`, `VMnet8`) | Virtual adapters connecting host machine to hypervisor virtual machines. | Home lab penetration testing, monitoring traffic between Kali Linux and Domain Controllers. |

---

## 3. Capture Filters vs. Display Filters

The distinction between capture and display filters is critical for memory management and analysis efficiency:

```text
Incoming Network Traffic
           │
           ▼
 [ Capture Filter (BPF) ] ──(No Match)──► DISCARDED (Never stored in memory)
           │ (Match)
           ▼
  [ Captured in PCAP Buffer ]
           │
           ▼
    [ Display Filter ]     ──(No Match)──► HIDDEN from GUI (Still saved in PCAP)
           │ (Match)
           ▼
 [ Rendered in Wireshark Pane ]
```

| Dimension | Capture Filter | Display Filter |
| :--- | :--- | :--- |
| **Execution Point** | Applied *before* packet capture begins (kernel/driver level). | Applied *after* packets are captured in memory. |
| **Syntax Standard** | Berkeley Packet Filter (BPF) syntax (e.g., `tcp port 80`). | Wireshark Display Filter language (e.g., `http.request.method == "GET"`). |
| **Data Retention** | Non-matching packets are permanently dropped. | Non-matching packets remain in buffer and can be viewed anytime. |
| **Primary Purpose** | Minimize PCAP file size and prevent buffer overflow on high-throughput links. | Isolate specific protocols, streams, anomalies, or transactions during analysis. |

---

## 4. Key Packet Analysis Workflows

### 4.1 Capturing the TCP Three-Way Handshake

TCP provides reliable, connection-oriented data transport via a three-way handshake:

```text
CLIENT                               SERVER
  │                                    │
  │──────── SYN (Seq = 0) ────────────►│  Step 1: Client initiates connection
  │                                    │
  │◄─────── SYN-ACK (Seq=0, Ack=1) ────│  Step 2: Server acknowledges & sets own Seq
  │                                    │
  │──────── ACK (Seq=1, Ack=1) ───────►│  Step 3: Client completes handshake
  │                                    │
  │====== TCP Connection Established ==│
```

#### Packet Analysis Details:
1. **SYN (Step 1):**
   - Flags: `SYN` set (`0x002`).
   - Sequence Number: `0` (Relative sequence number assigned by Wireshark for readability).
   - Acknowledgement Number: `0` (No data received from server yet).
   - TCP Segment Length: `0`.
2. **SYN-ACK (Step 2):**
   - Flags: `SYN, ACK` set (`0x012`).
   - Sequence Number: `0` (Server's initial relative sequence number).
   - Acknowledgement Number: `1` (Acknowledges client's SYN, which consumes 1 sequence number).
3. **ACK (Step 3):**
   - Flags: `ACK` set (`0x010`).
   - Sequence Number: `1`, Acknowledgement Number: `1`.
   - Completes handshake; application data can now be transmitted.

---

### 4.2 DNS Query and Response Analysis

DNS operates over UDP port 53 to resolve domain names to IP addresses:

```text
Client                                Resolver
  │                                       │
  │── Standard Query A/AAAA (TxID: 0x02) ─►│ (Question: example.com)
  │                                       │
  │◄─ Standard Query Response (TxID: 0x02)─│ (Answer: 93.184.215.14, TTL: 300)
```

#### Key Fields in DNS Packets:
- **Transaction ID (TxID):** A 16-bit identifier generated by the client to correlate query and response packets over stateless UDP.
- **Flags:** `0x0100` (Standard Query), `0x8180` (Standard Query Response, No error).
- **Questions:** Record type requested (`A` for IPv4, `AAAA` for IPv6, `MX` for Mail, `PTR` for Reverse lookup).
- **Answer RRs:** Resource records containing resolved IP addresses and Time-To-Live (**TTL**) caching limits.

---

### 4.3 Plaintext HTTP Traffic Inspection

Unencrypted HTTP (Port 80) transmits all data in plaintext:

```text
Client                                  Web Server
  │                                         │
  │── GET /index.html HTTP/1.1 ────────────►│ (Headers: Host, User-Agent, Cookie)
  │                                         │
  │◄── HTTP/1.1 200 OK ─────────────────────│ (Headers + Plaintext HTML Body)
```

#### Wireshark Inspection Capabilities:
- Request method, URI path, headers, and parameters are fully visible.
- Plaintext cookies (`Set-Cookie`, `Cookie`) and basic auth credentials can be extracted directly.
- **Follow TCP Stream:** Right-clicking any HTTP packet and selecting **Follow > TCP Stream** reconstructs the complete ASCII conversation between client and server.

---

### 4.4 TLS 1.3 / HTTPS Handshake & Encrypted Application Data

HTTPS encapsulates HTTP traffic inside a Transport Layer Security (TLS) tunnel (Port 443):

```text
Client                                  Server
  │                                       │
  │──────── TCP 3-Way Handshake ─────────►│
  │                                       │
  │──────── TLS Client Hello ────────────►│ (Advertises TLS versions, Cipher Suites, SNI)
  │                                       │
  │◄─────── TLS Server Hello ─────────────│ (Negotiates TLS 1.3, Key Exchange)
  │                                       │
  │◄─────── Certificate / Encrypted ──────│
  │                                       │
  │======== Encrypted Application Data ==│ (HTTP payload is completely opaque to Wireshark)
```

#### Security & Visibility Observations:
- **Server Name Indication (SNI):** The requested hostname is visible in plaintext inside the `Client Hello` extension even before encryption begins.
- **Encrypted Application Data (Content Type 23):** Once key exchange finishes, all subsequent HTTP methods, paths, headers, and payloads appear as encrypted application data. Wireshark cannot decode the HTTP payload without the session private key (Pre-Master Secret SSLKEYLOGFILE).

---

### 4.5 TCP Segmentation and Reassembly

Large application payloads (e.g., HTML documents, images) exceed the Maximum Transmission Unit (MTU / typical MSS ~1460 bytes):
- **Segmentation:** TCP splits the data stream into multiple segments with incrementing sequence numbers.
- **Reassembly:** The receiving TCP stack reconstructs segments in order using sequence numbers. Wireshark shows `[Reassembled TCP Segments]` in the packet details.

---

## 5. High-Yield Display Filters Cheat Sheet

### Protocol Filters
```text
tcp                      # Display all TCP packets
udp                      # Display all UDP packets
icmp                     # Display ICMP ping/traceroute packets
dns                      # Display DNS query and response packets
http                     # Display unencrypted HTTP traffic
tls                      # Display TLS handshake and record packets
arp                      # Display Address Resolution Protocol packets
```

### Port & IP Address Filters
```text
ip.addr == 192.168.10.10             # Traffic involving specific IP
ip.src == 192.168.10.50              # Traffic originating from source IP
ip.dst == 192.168.10.30              # Traffic destined to destination IP
tcp.port == 80                       # HTTP traffic on port 80
tcp.port == 443                      # HTTPS traffic on port 443
udp.port == 53                       # DNS traffic on port 53
tcp.port in {80 443 8080}            # Multiple ports matching
```

### HTTP-Specific Filters
```text
http.request                         # All outgoing HTTP requests
http.request.method == "POST"        # POST requests only
http.request.method == "GET"         # GET requests only
http.response.code == 200            # Successful HTTP 200 responses
http.response.code >= 400            # Client and server error responses (4xx, 5xx)
http.cookie                          # Packets containing Cookie headers
http contains "password"             # HTTP traffic containing string "password"
```

### TCP Flag & State Filters
```text
tcp.flags.syn == 1 && tcp.flags.ack == 0    # Initial SYN connection requests
tcp.flags.reset == 1                         # TCP RST (Reset) packets
tcp.analysis.retransmission                  # Retransmitted packets (packet loss detection)
```

---

## 6. Interview Questions & Key Takeaways

### What is the difference between Wireshark and Burp Suite?
- **Wireshark** is a passive network protocol analyzer operating across Layers 2–7, capturing all network protocols without modifying traffic.
- **Burp Suite** is an active Layer 7 (Application) web proxy designed specifically for intercepting, inspecting, and manipulating HTTP/HTTPS traffic between a browser and a web server.

### Why is HTTP payload data invisible in Wireshark when using HTTPS?
HTTPS encrypts HTTP data using TLS. While Wireshark captures the transport-layer TCP segments and can inspect the initial unencrypted TLS handshake (such as the SNI in Client Hello), the application data payload is encrypted ciphertext.

### What is the role of the DNS Transaction ID?
Because DNS uses connectionless UDP, the client generates a random 16-bit Transaction ID in its query so it can unambiguously match the resolver's asynchronous response to the correct query.
