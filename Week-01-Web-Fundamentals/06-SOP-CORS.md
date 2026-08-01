# Same-Origin Policy (SOP) & Cross-Origin Resource Sharing (CORS)

> Week 1 - Web Fundamentals
> Application Security Learning Notes

---

# Table of Contents

1. Introduction
2. What is an Origin?
3. Same-Origin Policy (SOP)
4. Why SOP Exists
5. Same-Origin Examples
6. Cross-Origin Requests
7. What is CORS?
8. How CORS Works
9. CORS Headers
10. Preflight Requests
11. Common CORS Misconfigurations
12. SOP vs CORS
13. CORS in Application Security
14. Common Mistakes
15. Interview Questions
16. Burp Suite Observation
17. AppSec Checklist
18. Key Takeaways
19. References

---

# Introduction

Modern web applications often communicate with multiple servers and APIs.

For example:

```
Frontend

https://app.example.com

↓

Backend API

https://api.example.com
```

The browser must decide:

"Should JavaScript running on one website be allowed to access another website's data?"

This is where:

- Same-Origin Policy (SOP)
- Cross-Origin Resource Sharing (CORS)

become important.

---

# What is an Origin?

An **origin** consists of three components:

```
Protocol

+

Host (Domain)

+

Port
```

Example

```
https://example.com:443
```

| Component | Value |
|------------|-------|
| Protocol | https |
| Host | example.com |
| Port | 443 |

All three must match for two URLs to be considered the same origin.

---

# Same-Origin Policy (SOP)

The **Same-Origin Policy (SOP)** is a browser security mechanism that prevents a web page from reading data from another origin.

Simply put,

A website can freely access its own resources, but it cannot read data from a different origin unless permission is granted.

---

# Why SOP Exists

Imagine you are logged into your online banking website.

```
https://mybank.com
```

At the same time, you visit

```
https://evil.com
```

Suppose SOP did not exist.

The attacker could execute:

```javascript
fetch("https://mybank.com/account")
```

The response could contain:

- Account Balance
- Transactions
- Personal Information

The attacker could steal all this data.

To prevent this, browsers enforce the Same-Origin Policy.

---

# Same-Origin Examples

## Example 1

```
https://example.com/profile

https://example.com/settings
```

Protocol

✓ Same

Host

✓ Same

Port

✓ Same

Result

✅ Same Origin

---

## Example 2

```
https://example.com

https://google.com
```

Different Host

❌ Different Origin

---

## Example 3

```
http://example.com

https://example.com
```

Different Protocol

❌ Different Origin

---

## Example 4

```
https://example.com:443

https://example.com:8080
```

Different Port

❌ Different Origin

---

# Cross-Origin Requests

A browser can still send requests to another origin.

However,

JavaScript usually **cannot read the response** unless the server explicitly allows it.

Example

```
evil.com

↓

GET https://bank.com/account

↓

Request reaches server

↓

Browser blocks JavaScript from reading the response
```

This is an important distinction.

SOP generally prevents JavaScript from reading cross-origin responses, not necessarily from sending the request.

---

# What is CORS?

**Cross-Origin Resource Sharing (CORS)** is a mechanism that allows a server to tell browsers:

"I trust this origin. It may access my resources."

Without CORS,

SOP blocks access.

With CORS,

the server grants permission.

---

# How CORS Works

Example

Frontend

```
https://app.company.com
```

Backend

```
https://api.company.com
```

Browser sends

```http
GET /users

Origin: https://app.company.com
```

Server replies

```http
Access-Control-Allow-Origin: https://app.company.com
```

Browser sees this header.

Result

✅ JavaScript may access the response.

---

# Common CORS Headers

---

## Access-Control-Allow-Origin

Specifies which origin is allowed.

Example

```http
Access-Control-Allow-Origin: https://app.company.com
```

---

## Access-Control-Allow-Methods

Specifies permitted HTTP methods.

Example

```http
Access-Control-Allow-Methods:

GET

POST

PUT

DELETE
```

---

## Access-Control-Allow-Headers

Specifies which request headers may be sent.

Example

```http
Access-Control-Allow-Headers:

Authorization

Content-Type
```

---

## Access-Control-Allow-Credentials

Allows cookies or authentication credentials.

Example

```http
Access-Control-Allow-Credentials: true
```

---

# Preflight Requests

Before sending certain requests, browsers perform a **preflight request**.

The browser first sends:

```http
OPTIONS /users
```

The server replies with allowed methods and headers.

If approved,

the browser sends the actual request.

Preflight is commonly used for:

- PUT
- PATCH
- DELETE
- Custom Headers
- Authorization Header

---

# Common CORS Misconfigurations

---

## Wildcard Origin

```http
Access-Control-Allow-Origin: *
```

Allows every website.

Safe only for public resources.

Dangerous when sensitive data is involved.

---

## Credentials with Wildcard

```
Access-Control-Allow-Origin: *

Access-Control-Allow-Credentials: true
```

Modern browsers reject this combination because it is insecure.

---

## Reflecting Arbitrary Origins

Suppose a request contains:

```
Origin:

https://evil.com
```

The server replies:

```
Access-Control-Allow-Origin:

https://evil.com
```

without validation.

This is a serious CORS misconfiguration because any origin may be trusted.

---

# SOP vs CORS

| Same-Origin Policy | CORS |
|--------------------|------|
| Browser Security Feature | Server Permission Mechanism |
| Enabled by default | Configured by server |
| Blocks JavaScript from reading unauthorized cross-origin responses | Allows selected cross-origin access |
| Improves security | Creates controlled exceptions |

---

# CORS in Application Security

Whenever testing CORS, ask:

Can I modify

```
Origin:
```

to

```
https://evil.com
```

Does the server respond with

```
Access-Control-Allow-Origin:

https://evil.com
```

If yes,

the server may trust arbitrary origins.

This could expose sensitive information.

---

# Common Mistakes

❌ Thinking SOP blocks all cross-origin requests.

SOP primarily blocks JavaScript from reading unauthorized responses.

---

❌ Thinking CORS is a browser feature.

CORS is configured by the server.

The browser enforces the policy.

---

❌ Assuming

```
Access-Control-Allow-Origin: *
```

is always insecure.

For completely public resources,

it may be acceptable.

For authenticated APIs,

it is usually inappropriate.

---

# Interview Questions

## What is Same-Origin Policy?

A browser security mechanism that prevents JavaScript from reading data from a different origin.

---

## What defines an origin?

Protocol

Host

Port

---

## What is CORS?

A server-controlled mechanism that allows selected origins to access resources across origins.

---

## Why does CORS exist?

To allow secure cross-origin communication while maintaining the protections of the Same-Origin Policy.

---

## What is a preflight request?

An HTTP OPTIONS request sent by the browser before certain cross-origin requests to determine whether the server permits them.

---

## Difference between SOP and CORS?

SOP blocks cross-origin access by default.

CORS allows the server to create controlled exceptions.

---

# Burp Suite Observation

Whenever testing CORS, inspect:

- Origin header
- Access-Control-Allow-Origin
- Access-Control-Allow-Credentials
- Access-Control-Allow-Headers
- Access-Control-Allow-Methods

Modify the Origin header and observe whether the server improperly trusts arbitrary domains.

---

# AppSec Checklist

Whenever testing CORS:

- Can I modify the Origin header?
- Does the server reflect arbitrary origins?
- Are credentials allowed?
- Is wildcard (*) used?
- Are sensitive APIs exposed cross-origin?
- Does the application unnecessarily trust third-party websites?

---

# Key Takeaways

- An origin consists of protocol, host, and port.
- Same-Origin Policy protects users by preventing unauthorized cross-origin access.
- CORS allows servers to selectively permit cross-origin requests.
- Browsers enforce SOP and CORS.
- Misconfigured CORS can expose sensitive data.
- Understanding SOP and CORS is essential for API security and modern web application testing.

---

# References

- MDN Web Docs – Same-Origin Policy
- MDN Web Docs – CORS
- OWASP Web Security Testing Guide
- OWASP API Security Top 10
- PortSwigger Web Security Academy
