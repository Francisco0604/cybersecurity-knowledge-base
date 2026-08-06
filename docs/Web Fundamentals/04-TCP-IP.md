# Module 1 – Web Fundamentals

---

# Lesson 4 – TCP/IP (Part 1)

---

## Objective

Understand the purpose of IP addresses, ports, TCP, and the TCP Three-Way Handshake before learning how web communication works.

---

## What is an IP Address?

An **IP (Internet Protocol) address** is a unique numerical identifier assigned to a device on a network.

It identifies where a device is located so other devices can communicate with it.

> **Analogy:** Think of an IP address as the physical address of a house.

**Example:**
- `192.168.1.15`
- `8.8.8.8`

Without an IP address, devices cannot locate one another on a network.

---

## Why is an IP Address Alone Not Enough?

Knowing the IP address tells us **where** the destination device is located.

However, a single device can run many different services simultaneously. For example, a web server, an SSH server, an email server, and a database server may all exist on the same IP address.

The operating system still needs to know **which service** should receive the incoming connection.

This is the purpose of **ports**.

---

## What is a Port?

A **port** is a logical communication endpoint that identifies a specific service or application running on a device.

> **Analogy:**
> - **IP Address** → Building Address  
> - **Port** → Apartment or Office Number

Without the correct port number, the operating system cannot determine which application should receive the connection.

### Common Ports

| Port | Protocol / Service |
| :--- | :----------------- |
| `80` | HTTP |
| `443` | HTTPS |
| `22` | SSH |
| `21` | FTP |
| `25` | SMTP |

---

## Default Ports

Most applications automatically use default ports.

**Examples:**
- `https://google.com` $\rightarrow$ Uses **Port 443**
- `http://example.com` $\rightarrow$ Uses **Port 80**

Therefore, users usually do not need to manually specify the port number.

---

## What is TCP?

**TCP** stands for **Transmission Control Protocol**.

TCP is a **connection-oriented** protocol. Before any application data is exchanged, both devices establish a reliable connection.

TCP focuses on:
- **Reliability**
- **Ordered delivery**
- **Error checking**
- **Retransmission of lost data**

---

## Why Do We Need TCP?

Networks are unreliable. Packets may:
- Be delayed
- Be dropped
- Arrive out of order
- Become corrupted

TCP ensures reliable communication by verifying that data reaches the destination correctly.

---

## TCP Three-Way Handshake

Before communication begins, TCP establishes a connection using three steps.

```text
Client                       Server
  |                            |
  |---------- SYN ------------>|
  |                            |
  |<------- SYN-ACK -----------|
  |                            |
  |---------- ACK ------------>|
  |                            |
[ Connection Established ]
```

### Step 1 – SYN

The client sends a `SYN` (Synchronize) packet to the server.

* **Meaning:** *"I would like to establish a connection."*

---

### Step 2 – SYN-ACK

The server responds with a `SYN-ACK` packet.

* **Meaning:** *"I received your request and I'm ready to communicate."*

---

### Step 3 – ACK

The client responds with an `ACK` packet.

* **Meaning:** *"I received your response."*

The connection is now established. Data transfer can begin.

---

## What Happens if the Server Does Not Respond?

If the client sends a `SYN` packet and receives no response:
- TCP waits for a short period.
- The `SYN` packet is retransmitted.
- This process repeats several times.
- If no response is received after multiple attempts, the connection attempt times out.

TCP does **not** immediately assume that the server is offline because packets can be delayed or lost during transmission.

---

## What Happens if the Final ACK is Lost?

If the server sends a `SYN-ACK` but never receives the client's `ACK`:
- The server assumes the `ACK` may have been lost.
- It retransmits the `SYN-ACK` packet several times.
- If no `ACK` is received after multiple attempts, the server times out and closes the half-open connection.

This behaviour makes TCP reliable even when packets are lost.

---

## Key Concepts

- **IP addresses** identify devices.
- **Ports** identify applications or services.
- **TCP** provides reliable communication.
- **TCP** establishes a connection before sending data.
- **TCP** retransmits packets if acknowledgements are not received.

---

## Important Terms

### IP Address
A unique numerical address assigned to a device on a network.

### Port
A logical communication endpoint used to identify a service or application.

### TCP
A reliable, connection-oriented transport protocol responsible for delivering data correctly between devices.

### SYN
The first packet sent to request a TCP connection.

### SYN-ACK
The server's acknowledgement indicating it is ready to establish a connection.

### ACK
The client's acknowledgement confirming the connection.

---

## Practical Observations

- A browser needs both an IP address and a port number to communicate with a web server.
- HTTPS typically uses port 443.
- HTTP typically uses port 80.
- TCP retries failed connection attempts before timing out.
- Lost packets do not immediately terminate a connection because TCP provides retransmission mechanisms.

---

## Interview Questions

### What is the difference between an IP address and a port?
An IP address identifies a device on a network, while a port identifies the specific service or application running on that device.

---

### Why is TCP called a connection-oriented protocol?
Because it establishes a connection between two devices using the TCP Three-Way Handshake before exchanging application data.

---

### What is the TCP Three-Way Handshake?
The process used to establish a TCP connection:
1. **SYN**
2. **SYN-ACK**
3. **ACK**

Only after this handshake can data transmission begin.

---

### What happens if the server does not respond to a SYN packet?
The client retransmits the `SYN` packet several times before eventually timing out if no response is received.

---

### What happens if the client's final ACK is lost?
The server retransmits the `SYN-ACK` packet several times while waiting for the client's acknowledgement. If no `ACK` is received, the server eventually times out and closes the incomplete connection.

---

## My Understanding

- An IP address tells me where the destination device is located.
- A port tells me which service or application on that device I want to communicate with.
- TCP establishes a reliable connection before any data is exchanged.
- The TCP Three-Way Handshake ensures both devices are ready to communicate.
- TCP handles lost packets by retransmitting them instead of immediately assuming the connection has failed.

---

## Lesson Summary

- **IP addresses** identify devices on a network.
- **Ports** identify specific services running on those devices.
- **TCP** is responsible for reliable communication.
- **The TCP Three-Way Handshake** establishes a connection before data transfer.
- **TCP** uses acknowledgements and retransmissions to ensure reliable delivery even when packets are lost.
