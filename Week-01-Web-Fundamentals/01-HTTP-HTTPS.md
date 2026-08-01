# HTTP & HTTPS

> Week 1 - Web Fundamentals  
> Application Security Learning Notes

---

# Table of Contents

1. Introduction
2. What is HTTP?
3. HTTP Architecture
4. Characteristics of HTTP
5. HTTP Request & Response Lifecycle
6. HTTP vs HTTPS
7. TLS Handshake (HTTPS)
8. Ports Used
9. Why HTTPS Matters
10. HTTP in Application Security
11. Real-World Examples
12. Interview Questions
13. Key Takeaways
14. References

---

# Introduction

Every interaction between a client (browser, mobile app, API client) and a web server happens using the **HyperText Transfer Protocol (HTTP)**.

Whenever you:

- Open a website
- Log in
- Submit a form
- Upload a file
- Call an API

your browser sends an HTTP request to a server, and the server replies with an HTTP response.

As an Application Security Engineer, understanding HTTP is essential because almost every web vulnerability (SQL Injection, XSS, CSRF, IDOR, SSRF, etc.) involves manipulating HTTP requests or responses.

---

# What is HTTP?

HTTP (**HyperText Transfer Protocol**) is an application-layer protocol that allows clients and servers to communicate over a network.

It defines:

- How a request is sent.
- How a response is returned.
- Which methods can be used.
- Which status codes indicate success or failure.

HTTP is **stateless**, meaning the server treats every request as independent.

Without cookies or sessions, the server does not remember previous requests.

---

# HTTP Architecture

```
Browser / Client
        │
        │ HTTP Request
        ▼
Web Server
        │
        │ Process Request
        ▼
Database / Backend Logic
        │
        │ HTTP Response
        ▼
Browser
```

Example:

You visit

```
https://github.com
```

Your browser sends:

```
GET /
```

The server replies:

```
HTTP/1.1 200 OK
```

along with the HTML content.

---

# Characteristics of HTTP

## 1. Stateless

Each request is independent.

Example:

```
Request 1
↓

Login

↓

Server responds
```

Later:

```
Request 2

↓

Open Profile
```

The server does **not** automatically remember the login.

This is why sessions or JWTs are required.

---

## 2. Client-Server Model

HTTP follows a client-server architecture.

Client:

- Browser
- Mobile App
- Postman
- Burp Suite

Server:

- Apache
- Nginx
- IIS
- Node.js
- Go Backend

The client always initiates communication.

---

## 3. Request-Response Protocol

Communication always follows this pattern:

```
Client

↓

Request

↓

Server

↓

Response

↓

Client
```

The server never sends data unless a request is made.

---

# HTTP Request & Response Lifecycle

Imagine a user logs in.

### Step 1

Browser sends

```
POST /login
```

### Step 2

Server validates credentials.

### Step 3

Server returns

```
HTTP/1.1 200 OK
```

along with a session cookie or JWT.

### Step 4

Browser stores the session or token.

### Step 5

Future requests include the session cookie or Authorization header.

---

# HTTP vs HTTPS

## HTTP

HTTP sends data in plaintext.

Example:

```
username=rahul
password=Password123
```

Anyone intercepting network traffic can read it.

---

## HTTPS

HTTPS encrypts communication using **TLS (Transport Layer Security)**.

Example:

```
Encrypted Data

↓

Only Client and Server can read it.
```

Even if an attacker captures network traffic, they cannot read its contents without breaking the encryption.

---

# TLS Handshake (Simplified)

HTTPS begins with a TLS handshake.

```
Browser

↓

Hello

↓

Server sends certificate

↓

Browser verifies certificate

↓

Encryption keys established

↓

Secure communication begins
```

After the handshake, all HTTP traffic is encrypted.

---

# Default Ports

| Protocol | Port |
|-----------|------|
| HTTP | 80 |
| HTTPS | 443 |

---

# Why HTTPS Matters

HTTPS provides three major security properties.

## Confidentiality

Data is encrypted.

Attackers cannot read intercepted traffic.

---

## Integrity

Data cannot be modified during transmission without detection.

---

## Authentication

The server proves its identity using a digital certificate.

This helps prevent impersonation attacks.

---

# HTTP in Application Security

Every vulnerability an AppSec engineer tests ultimately involves HTTP requests.

Examples:

## SQL Injection

Modify request parameters.

```
POST /login
```

```
username=' OR 1=1--
```

---

## XSS

Inject JavaScript into user input.

```
GET /search?q=<script>alert(1)</script>
```

---

## IDOR

Modify object identifiers.

```
GET /users/101
```

↓

```
GET /users/102
```

---

## File Upload

Intercept upload request.

Modify:

- Filename
- MIME Type
- Extension

---

## Authentication Testing

Modify:

- Authorization header
- Cookies
- JWT
- Session ID

---

# Real-World Example

Suppose you log into GitHub.

The browser sends:

```
POST /login
```

GitHub verifies your credentials.

If successful, GitHub returns:

- Session Cookie

or

- JWT (depending on implementation)

Every future request includes that authentication information.

---

# Common Misconceptions

### HTTP remembers users.

❌ False

HTTP is stateless.

Sessions or JWTs maintain authentication.

---

### HTTPS hides the URL.

❌ False

HTTPS encrypts the request and response, but some metadata (such as the destination domain during connection establishment, depending on protocol features) is still observable. It primarily protects the contents of the communication.

---

### HTTPS makes an application secure.

❌ False

HTTPS only secures communication.

Applications can still be vulnerable to:

- SQL Injection
- XSS
- IDOR
- CSRF
- SSRF

---

# Interview Questions

## What is HTTP?

HTTP is an application-layer protocol used for communication between clients and servers.

---

## What is HTTPS?

HTTPS is HTTP protected using TLS encryption.

---

## Why is HTTP stateless?

Because the server treats every request independently and does not automatically remember previous requests.

---

## Why do websites use HTTPS?

To provide:

- Confidentiality
- Integrity
- Authentication

---

## What is the difference between HTTP and HTTPS?

| HTTP | HTTPS |
|------|-------|
| Plaintext communication | Encrypted communication |
| Port 80 | Port 443 |
| No TLS | Uses TLS |
| Vulnerable to interception | Protects data in transit |

---

# AppSec Perspective

Whenever you intercept an HTTP request, ask yourself:

- Can I modify parameters?
- Can I change headers?
- Can I replay the request?
- Can I remove authentication?
- Can I bypass authorization?
- Is sensitive information exposed?
- Is HTTPS being used correctly?

Every penetration test starts with understanding the HTTP request.

---

# Key Takeaways

- HTTP is the foundation of all web communication.
- HTTP is stateless.
- HTTPS uses TLS to encrypt communication.
- Every web vulnerability involves manipulating HTTP requests or responses.
- Understanding HTTP is essential before learning Burp Suite or web exploitation.

---

# References

- RFC 9110 – HTTP Semantics
- OWASP Web Security Testing Guide (WSTG)
- PortSwigger Web Security Academy