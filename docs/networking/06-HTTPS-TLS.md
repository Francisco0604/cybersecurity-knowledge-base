# HTTPS and TLS

## Overview

HTTPS is HTTP protected by TLS (Transport Layer Security).

Typical flow:

```text
DNS → TCP 3-way handshake → TLS handshake → Encrypted application data
```

Plain HTTP commonly uses TCP port `80`, while HTTPS commonly uses TCP port `443`.

```text
HTTP  → TCP → plaintext HTTP
HTTPS → TCP → TLS → encrypted HTTP
```

## 1. What TLS Provides

TLS provides three important security properties:

### Confidentiality
TLS encrypts protected application data so network observers cannot normally read the HTTP contents.

### Integrity
TLS protects against undetected modification. If protected data is altered, the receiver should detect the change and reject the altered data.

### Authentication
TLS helps verify the identity of the server, commonly through certificates.

---

## 2. TLS Handshake

Before protected application data is exchanged, the client and server perform a TLS handshake.

```text
Client                         Server
  │
  │ Client Hello
  │──────────────────────────►
  │
  │ Server Hello
  │◄──────────────────────────
  │
  │ Key exchange / handshake
  │◄─────────────────────────►
  │
  │ Encrypted Application Data
  │◄─────────────────────────►
```

The handshake allows both sides to negotiate parameters and establish the cryptographic context needed for secure communication.

---

## 3. Client Hello

The Client Hello is the client's initial TLS handshake message.

The captured Client Hello contained:

```text
SNI: example.com
Supported versions: TLS 1.3, TLS 1.2
Cipher suites: 20 offered
```

It also contained other TLS extensions and parameters.

### TLS version fields

The capture showed older compatibility fields such as TLS 1.0/TLS 1.2 in the record and ClientHello structures. These should not automatically be interpreted as the negotiated TLS version.

For modern TLS, the `supported_versions` extension is the important field.

The capture showed:

```text
Client supports:
TLS 1.3
TLS 1.2
```

The Server Hello selected TLS 1.3.

Therefore:

```text
Negotiated TLS version: TLS 1.3
```

---

## 4. SNI — Server Name Indication

The Client Hello contained:

```text
server_name: example.com
```

SNI tells the server which hostname the TLS connection is intended for.

This is useful because the same server/server infrastructure can serve multiple domains.

SNI identifies the hostname for the TLS connection. It does not specify the HTTP resource.

For example:

```text
SNI: example.com
```

identifies the hostname, while:

```http
GET /login HTTP/1.1
Host: example.com
```

identifies the HTTP resource.

---

## 5. Cipher Suites

The Client Hello offered multiple cipher suites so the server could select a cryptographic option supported by both sides.

The captured Server Hello selected:

```text
TLS_AES_256_GCM_SHA384 (0x1302)
```

In TLS 1.3, cipher suites primarily specify the symmetric encryption algorithm and hash. Key exchange parameters are negotiated separately.

---

## 6. Server Hello

The Server Hello is the server's response to the Client Hello.

The captured Server Hello showed:

```text
Negotiated TLS version: TLS 1.3
Cipher suite: TLS_AES_256_GCM_SHA384
Key exchange group: x25519
```

Conceptually:

```text
Client Hello
    ↓
Client offers options
    ↓
Server Hello
    ↓
Server selects compatible options
```

---

## 7. Key Exchange — X25519

The Server Hello contained:

```text
Extension: key_share
x25519
```

`x25519` is a key-exchange group used in TLS 1.3.

For this lab, the important point is that key-exchange parameters are used to establish the cryptographic material required for protected communication.

The mathematical details of X25519 are outside the scope of this introductory lab.

---

## 8. Compression

The Server Hello showed:

```text
Compression Method: null (0)
```

TLS 1.3 does not use TLS-level compression, so no TLS-level compression was negotiated.

---

## 9. Encrypted Application Data

After the handshake, the capture showed:

```text
TLSv1.3 Record Layer:
Application Data Protocol: Hypertext Transfer Protocol
```

This means the HTTP communication is being carried inside TLS.

Instead of seeing:

```http
GET / HTTP/1.1
Host: example.com
```

the packet capture shows encrypted TLS application data.

Conceptually:

```text
HTTP
 ↓
TLS encryption
 ↓
Encrypted Application Data
 ↓
TCP
```

---

## 10. Why Wireshark Can Identify HTTP Without Showing Its Contents

Wireshark can identify protocol information from captured traffic and dissect the protocol layers.

In the captured packet it displayed:

```text
TLSv1.3 Record Layer:
Application Data Protocol: Hypertext Transfer Protocol
```

However, it did not display the actual HTTP request in plaintext.

This distinction is important:

```text
Protocol identification ≠ Reading encrypted contents
```

Wireshark can identify the protocol while the application data remains encrypted.

Wireshark is analyzing the traffic; it is not telling the client which protocol to use.

---

## 11. TLS Over TCP

The captured traffic has multiple layers:

```text
HTTP
 ↓
TLS
 ↓
TCP
 ↓
IP
```

TCP provides reliable transport, while TLS provides security for the application data.

This is why the responsibilities should be kept separate:

```text
TCP
└── Reliability
    ├── sequencing
    ├── acknowledgements
    └── retransmission

TLS
├── confidentiality
├── integrity
└── authentication
```

---

## 12. TCP Segmentation of TLS Data

The captured encrypted application data was carried across multiple TCP segments.

Wireshark showed:

```text
[4 Reassembled TCP Segments (4214 bytes)]

#13 (1167 bytes)
#14 (1300 bytes)
#15 (1300 bytes)
#16 (447 bytes)
```

This demonstrates:

```text
TLS Application Data
        ↓
TCP segmentation
        ↓
Multiple TCP segments
        ↓
Wireshark reassembly
        ↓
TLS encrypted data
```

TCP reassembly does not decrypt TLS contents.

---

## 13. HTTPS Packet Flow Observed in the Lab

```text
DNS resolution
      ↓
TCP SYN
      ↓
TCP SYN, ACK
      ↓
TCP ACK
      ↓
TLS Client Hello
      ↓
TLS Server Hello
      ↓
TLS handshake / key establishment
      ↓
TLS 1.3 Application Data
      ↓
Encrypted HTTP communication
```

Important values observed in the capture:

```text
TLS version: TLS 1.3
SNI: example.com
Cipher suite: TLS_AES_256_GCM_SHA384
Key exchange group: x25519
Source port: 443
```

---

## 14. HTTP vs HTTPS in Wireshark

### Plain HTTP

The HTTP lab used:

```text
http://example.com
```

Wireshark could display:

```http
GET / HTTP/1.1
Host: example.com
```

and the returned HTML in plaintext.

### HTTPS

The TLS lab used traffic over port `443`.

Wireshark displayed:

```text
TLSv1.3
Application Data
Protocol: Hypertext Transfer Protocol
```

but the actual HTTP request was not visible in plaintext because it was protected by TLS.

Comparison:

```text
HTTP:

TCP
 ↓
HTTP
 ↓
Plaintext


HTTPS:

TCP
 ↓
TLS
 ↓
Encrypted HTTP
```

---

## 15. Important TLS Fields Observed

| Field | Meaning |
|---|---|
| Client Hello | Client's initial TLS handshake message |
| Server Hello | Server's response and selected parameters |
| SNI | Hostname intended for the TLS connection |
| supported_versions | TLS versions supported/negotiated |
| Cipher Suites | Cryptographic options offered by the client |
| Cipher Suite | Selected symmetric encryption/hash combination |
| key_share | Key-exchange information |
| x25519 | Key-exchange group observed in the capture |
| Application Data | TLS-protected application-layer data |
| TLS 1.3 | Negotiated TLS version |

---

## 16. Key Takeaways

- HTTPS is HTTP protected by TLS.
- HTTP commonly uses port `80`.
- HTTPS commonly uses port `443`.
- TCP establishes the reliable transport connection before TLS.
- The TLS handshake negotiates parameters for secure communication.
- Client Hello contains supported versions, cipher suites, SNI, and other extensions.
- Server Hello communicates selected parameters.
- The captured connection negotiated TLS 1.3.
- The selected cipher suite was `TLS_AES_256_GCM_SHA384`.
- The captured key-exchange group was `x25519`.
- SNI identified `example.com`.
- TLS provides confidentiality, integrity, and server authentication.
- HTTP data carried inside TLS is encrypted.
- Wireshark can identify protocol information without being able to read encrypted HTTP contents.
- TLS application data can be split across multiple TCP segments.
- TCP reassembly does not decrypt TLS data.

---

## Conclusion

The HTTPS/TLS lab demonstrated how HTTP is protected when transmitted over TLS.

The captured Client Hello showed that the client supported TLS 1.3 and TLS 1.2 and offered multiple cipher suites. The Server Hello selected TLS 1.3, `TLS_AES_256_GCM_SHA384`, and the `x25519` key-exchange group.

After the TLS handshake, Wireshark identified encrypted application data associated with HTTP. Unlike the plain HTTP lab, the actual HTTP request could not normally be viewed in plaintext.

This demonstrates the key difference between HTTP and HTTPS and provides the TLS foundation needed to understand how tools such as Burp Suite can inspect HTTPS traffic at the application layer in an authorized testing environment.
