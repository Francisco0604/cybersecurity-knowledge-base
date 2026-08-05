# Module 1 – Web Fundamentals

---

# Lesson 1 – How a Website Works

## Objective

Understand how a browser communicates with a web server and where Burp Suite fits into the communication process.

---

## Website Communication Flow

```text
User
↓
Browser
↓
DNS Lookup
↓
IP Address
↓
TCP Connection
↓
TLS Handshake (HTTPS)
↓
HTTP Request
↓
Web Server
↓
HTTP Response
↓
Browser Renders the Website
```

---

## Why is this Important?

Every web application follows this communication flow.

Before learning Burp Suite, it is important to understand what the browser is sending and receiving.

Burp Suite does not create requests.

It intercepts requests that are already being sent between the browser and the web server.

---

# What is Burp Suite?

Burp Suite is an **Intercepting Proxy** used during web application security testing.

Instead of the browser sending HTTP requests directly to the web server, it sends them to Burp first.

Burp allows the tester to:

- View requests
- View responses
- Modify requests
- Modify responses (where applicable)
- Replay requests
- Analyze web traffic

---

## Communication Flow with Burp

```text
Firefox
↓
Burp Suite (Proxy)
↓
Internet
↓
Web Server
```

---

## Why Does Burp Sit Between the Browser and the Server?

Burp acts as a middleman.

Instead of allowing Firefox to communicate directly with the server, Firefox is configured to send all web traffic to Burp.

Burp then decides whether to:

- Stop the request
- Modify the request
- Forward the request
- Record the request

---

# Browser Proxy Configuration

Normally Firefox sends requests directly to websites.

After configuring Burp as the proxy:

```text
Browser
↓
127.0.0.1:8080 (Burp Suite)
↓
Destination Website
```

`127.0.0.1` refers to the local computer (localhost).

Port `8080` is the listening port used by Burp's Proxy by default.

---

# Intercept ON vs Intercept OFF

## Intercept ON

- Every request pauses inside Burp.
- The tester can inspect or modify it.
- The request reaches the server only after clicking **Forward**.

**Flow:**

```text
Browser
↓
Burp
↓
STOP
↓
Forward
↓
Server
```

---

## Intercept OFF

- Requests still pass through Burp.
- Burp automatically forwards them.
- All requests and responses are still recorded in **HTTP History**.

**Flow:**

```text
Browser
↓
Burp
↓
Server
```

---

# What Happens if Burp is Closed?

If Firefox is still configured to use Burp as its proxy:

```text
Browser
↓
127.0.0.1:8080 (No application listening)
```

The browser cannot send requests.

**Result:**

- Proxy Connection Failed
- Unable to Connect
- Website does not load

**Reason:**

Firefox is attempting to send requests to Burp, but Burp is not running.

---

# Key Terms

## Browser

Software used to access websites.

**Examples:**
- Firefox
- Chrome
- Edge

---

## Web Server

A computer that hosts websites and responds to HTTP requests.

**Examples:**
- Apache
- Nginx
- IIS

---

## Proxy

An intermediary that receives requests before forwarding them to their destination.

Burp Suite functions as an intercepting proxy.

---

## Localhost

`127.0.0.1`

A special IP address that always refers to the current computer.

---

## Port

A logical communication endpoint used by applications.

Burp listens on port `8080` by default.

---

# Practical Observation

When Intercept is OFF:
- Websites load normally.
- Burp still captures every request.

When Intercept is ON:
- Websites pause until the request is forwarded.

When Burp is closed:
- Websites fail to load because Firefox still attempts to use Burp as its proxy.

---

# Interview Questions

### What is Burp Suite?

Burp Suite is an intercepting web proxy that allows security testers to inspect, modify, replay and analyze HTTP/HTTPS traffic between a browser and a web server.

---

### Why does Burp sit between the browser and the web server?

Because the browser is configured to use Burp as its proxy.

This allows Burp to inspect and manipulate requests before they reach the destination server.

---

### What happens if Burp is closed while the browser is still configured to use it?

The browser attempts to send requests to the configured proxy (127.0.0.1:8080).

Since Burp is not running, nothing is listening on that address, causing requests to fail.

---

# Lesson Summary

- Every website communication follows a sequence before a page loads.
- Burp acts as an intercepting proxy between the browser and the web server.
- Intercept ON pauses requests.
- Intercept OFF forwards requests automatically while still recording them.
- If Burp is closed, the browser cannot communicate through the configured proxy.
