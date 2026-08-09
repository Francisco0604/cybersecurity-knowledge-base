# Module 1 – Web Fundamentals

---

# Lesson 3 – DNS Hierarchy

## Objective

Understand how DNS servers work together to resolve a domain name into an IP address and why the DNS hierarchy exists.

---

## Why Doesn't One DNS Server Know Everything?

The Internet contains hundreds of millions of domain names.

Maintaining a single DNS server with every domain and its IP address would be inefficient and difficult to manage.

Instead, DNS is distributed into multiple levels, with each server responsible for a specific part of the namespace.

---

## DNS Resolution Flow

```text
Browser
  ↓
Local DNS Resolver
  ↓
Root DNS Server
  ↓
Top-Level Domain (TLD) DNS Server
  ↓
Authoritative DNS Server
  ↓
Returns IP Address
  ↓
Browser connects to the Web Server
```

---

## Local DNS Resolver

The Local DNS Resolver is the first DNS server contacted by the browser.

It is usually provided by:

- **Internet Service Provider (ISP)**
- **Google Public DNS** (`8.8.8.8`)
- **Cloudflare DNS** (`1.1.1.1`)

**Responsibilities:**

- Receives DNS queries from the client.
- Checks its cache for an existing record.
- If the record is not available, it queries other DNS servers.

---

## Root DNS Server

The Root DNS Server is the highest level of the DNS hierarchy.

It does **not** know the IP address of every website.

Its responsibility is to direct the resolver to the correct Top-Level Domain (TLD) server.

**Example:**

- **Request:** `google.com`
- **Response:** Ask the `.com` TLD server.

---

## Top-Level Domain (TLD) DNS Server

A TLD server manages information about domain extensions.

Examples include:

- `.com`
- `.org`
- `.net`
- `.edu`
- `.gov`
- `.in`

The TLD server does not know the final IP address.

Instead, it tells the resolver which Authoritative DNS Server manages the requested domain.

---

## Authoritative DNS Server

The Authoritative DNS Server stores the official DNS records for a domain.

It contains the actual IP address associated with the requested domain.

**Example:**

`google.com`  →  `142.xxx.xxx.xxx`

The resolver returns this IP address to the browser.

---

## Complete DNS Lookup Process

1. The user enters a domain name in the browser.
2. The browser sends a query to the Local DNS Resolver.
3. The resolver checks its cache.
4. If no cached record exists, it contacts a Root DNS Server.
5. The Root Server directs it to the appropriate TLD Server.
6. The TLD Server identifies the Authoritative DNS Server.
7. The Authoritative DNS Server returns the domain's IP address.
8. The Local Resolver caches the result.
9. The IP address is returned to the browser.
10. The browser establishes a connection with the destination server.

---

## DNS Caching

To improve performance, DNS responses are cached.

Cached responses reduce:

- Lookup time
- Network traffic
- Load on DNS infrastructure

Caching may occur in:

- **Browser**
- **Operating System**
- **Router** (sometimes)
- **Local DNS Resolver**

---

## What Happens if Root DNS Servers Become Unavailable?

Root DNS Servers are responsible for directing DNS resolvers to the appropriate Top-Level Domain (TLD) servers.

If they become unavailable:

- Cached DNS records continue working until they expire.
- New DNS lookups cannot be resolved.
- Expired cache entries cannot be refreshed.
- Over time, more websites become unreachable as cached records expire.

---

## Key Concepts

- DNS is hierarchical.
- Different DNS servers have different responsibilities.
- The Root Server does not know website IP addresses.
- The TLD Server identifies the correct Authoritative DNS Server.
- The Authoritative DNS Server provides the final IP address.
- DNS caching significantly improves performance.

---

## Important Terms

### Local DNS Resolver
The first DNS server contacted by a client. It performs DNS lookups on behalf of the browser.

### Root DNS Server
The top level of the DNS hierarchy. It directs queries to the appropriate TLD server.

### Top-Level Domain (TLD)
The last portion of a domain name (e.g., `.com`, `.org`, `.net`, `.in`).

### Authoritative DNS Server
The DNS server that stores the official DNS records for a domain.

### DNS Cache
Temporary storage used to save previously resolved domain names and their IP addresses.

---

## Practical Observations

- Most DNS queries never reach the Root Server because cached records are often available.
- DNS caching makes repeated visits to websites much faster.
- Root DNS Servers direct traffic; they do not store every website's IP address.

---

## Interview Questions

### Why is DNS hierarchical?
A hierarchical structure distributes responsibility among multiple DNS servers, making the system scalable, efficient, and easier to manage.

---

### Does the Root DNS Server know Google's IP address?
No. The Root DNS Server only directs queries to the correct Top-Level Domain (TLD) server.

---

### Which DNS server knows the actual IP address of a website?
The Authoritative DNS Server.

---

### What would happen if Root DNS Servers disappeared?
Cached DNS records would continue working temporarily. However, new DNS lookups and expired cache entries could no longer be resolved, causing more websites to become unreachable over time.

---

## My Understanding

- A single DNS server cannot store information for every website on the Internet.
- DNS uses a hierarchy where each server has a specific responsibility.
- The Local DNS Resolver asks the Root Server, which directs it to the TLD Server, which then points it to the Authoritative DNS Server.
- The Authoritative DNS Server returns the actual IP address.
- DNS caching reduces the need for repeated lookups and improves browsing speed.

---

## Lesson Summary

- DNS uses a hierarchical structure to resolve domain names.
- Local DNS Resolvers perform lookups on behalf of clients.
- Root Servers direct queries to the correct TLD servers.
- TLD servers identify the appropriate Authoritative DNS Server.
- Authoritative DNS Servers provide the final IP address.
- DNS caching improves performance and reduces unnecessary network traffic.
