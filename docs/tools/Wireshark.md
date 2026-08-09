# Wireshark

## Purpose

Wireshark is a network protocol analyzer used to capture, inspect, and analyze network traffic in real time.

It allows security professionals, network engineers, and penetration testers to observe how devices communicate over a network.

Unlike Burp Suite, which primarily focuses on HTTP and HTTPS traffic, Wireshark captures traffic from all supported network protocols.

---

# Common Use Cases

- Network troubleshooting
- Packet analysis
- TCP/IP analysis
- DNS analysis
- HTTP/HTTPS inspection
- Malware traffic analysis
- Network forensics
- Security investigations

---

# How Wireshark Works

Wireshark listens on a selected network interface and captures packets entering or leaving that interface.

Every packet can then be inspected in detail.

---

# Network Interfaces

A network interface is a communication point between a computer and a network.

Examples:

### Wi-Fi

Used when the computer communicates over a wireless network.

This is typically the interface used to capture Internet traffic from the host machine.

---

### Ethernet

Used for wired network connections.

---

### Loopback Adapter

Captures traffic that never leaves the local machine.

Common examples:

- localhost
- 127.0.0.1
- Docker containers
- Local web servers

---

### VMware Network Adapters

Virtual network interfaces created by VMware.

Examples:

VMnet1

Typically used for Host-Only networking.

VMnet8

Typically used for NAT networking.

These adapters allow communication between virtual machines and the host.

---

# Capture Filter vs Display Filter

These two concepts are commonly confused.

## Capture Filter

Capture Filters determine which packets Wireshark records.

Packets that do not match the filter are never captured.

Example:

```
tcp
```

Capture only TCP traffic.

---

## Display Filter

Display Filters determine which captured packets are shown.

All packets are still captured.

The filter only hides packets that do not match.

Example:

```
tcp
```

Display only TCP packets.

---

# Difference

Capture Filter

↓

Controls what gets recorded.

Display Filter

↓

Controls what gets displayed.

---

# Common Display Filters

Display only TCP:

```
tcp
```

Display only UDP:

```
udp
```

Display only DNS:

```
dns
```

Display only HTTP:

```
http
```

Display HTTPS traffic:

```
tcp.port == 443
```

Display HTTP traffic:

```
tcp.port == 80
```

---

# Common Toolbar Buttons

▶ Start Capture

Begins capturing packets.

---

■ Stop Capture

Stops packet capture.

---

🗑 Clear Packets

Removes the currently displayed capture from memory.

---

# Practical Observations

- Every packet shown in Wireshark represents network communication.
- Multiple protocols can be captured simultaneously.
- Filtering traffic is essential because real-world captures often contain thousands of packets.
- Choosing the correct network interface is critical for capturing the desired traffic.

---

# Interview Questions

## What is Wireshark?

Wireshark is a network protocol analyzer used to capture and inspect network traffic in real time.

---

## What is the difference between a Capture Filter and a Display Filter?

A Capture Filter determines which packets are recorded.

A Display Filter determines which captured packets are displayed.

---

## Why is selecting the correct network interface important?

Wireshark can only capture traffic that passes through the selected interface.

Choosing the wrong interface may result in capturing little or no relevant traffic.

---

# My Understanding

- Wireshark allows me to observe network traffic in real time.
- The network interface determines which traffic can be captured.
- Capture Filters reduce the amount of traffic recorded.
- Display Filters help analyze captured traffic by hiding unrelated packets.
- Wireshark can analyze much more than HTTP, making it an essential networking and cybersecurity tool.

---

# Lesson Summary

- Wireshark is a packet capture and analysis tool.
- It captures traffic from selected network interfaces.
- Capture Filters affect recording.
- Display Filters affect visibility.
- Selecting the correct interface is the first step in any packet analysis.
