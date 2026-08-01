# Cookies & Sessions

> Week 1 - Web Fundamentals
> Application Security Learning Notes

---

# Table of Contents

1. Introduction
2. What are Cookies?
3. Why are Cookies Needed?
4. Cookie Lifecycle
5. Types of Cookies
6. Cookie Attributes
7. What is a Session?
8. Session Lifecycle
9. Cookies vs Sessions
10. Authentication Flow
11. Security Risks
12. Cookies & Sessions in Application Security
13. Common Mistakes
14. Interview Questions
15. Burp Suite Observation
16. AppSec Checklist
17. Key Takeaways
18. References

---

# Introduction

HTTP is a **stateless protocol**, meaning the server treats every request independently.

For example,

You log into a website.

```
POST /login
```

The server authenticates you.

Now you visit

```
GET /profile
```

How does the server know that this request belongs to you?

The answer is:

- Cookies
- Sessions
- JWT (Modern Applications)

Cookies and sessions allow a web application to remember users across multiple requests.

---

# What is a Cookie?

A cookie is a **small piece of data stored in the user's browser**.

The server sends a cookie to the browser, and the browser automatically includes it in future requests.

Example

Server Response

```http
HTTP/1.1 200 OK

Set-Cookie: session=abc123
```

Browser stores

```
session=abc123
```

Future Request

```http
GET /profile

Cookie: session=abc123
```

The browser sends the cookie automatically.

---

# Why are Cookies Needed?

Imagine logging into GitHub.

Without cookies

```
Request 1

↓

Login

↓

Success
```

Next request

```
Open Profile
```

The server would not know who you are.

You would have to log in again for every page.

Cookies solve this problem.

---

# Cookie Lifecycle

Step 1

User logs in.

↓

Step 2

Server authenticates credentials.

↓

Step 3

Server sends

```http
Set-Cookie: session=abc123
```

↓

Step 4

Browser stores cookie.

↓

Step 5

Browser automatically sends

```http
Cookie: session=abc123
```

with every future request.

↓

Step 6

Server identifies the user.

---

# Types of Cookies

## Session Cookie

Exists only while the browser is open.

Deleted when the browser closes.

Example

```
session=abc123
```

---

## Persistent Cookie

Stored until a specified expiration time.

Example

```
Remember Me
```

Allows users to remain logged in for days or weeks.

---

# Cookie Attributes

Cookies have attributes that improve security.

---

## HttpOnly

Example

```http
Set-Cookie: session=abc123; HttpOnly
```

Purpose

JavaScript cannot access the cookie.

Protects against cookie theft during XSS attacks.

---

## Secure

Example

```http
Set-Cookie: session=abc123; Secure
```

Purpose

Cookie is sent only over HTTPS.

Protects against interception over insecure connections.

---

## SameSite

Controls when browsers send cookies with cross-site requests.

Example

```http
Set-Cookie: session=abc123; SameSite=Lax
```

Values

### Strict

Cookie sent only for same-site requests.

Most secure.

---

### Lax

Cookie sent for most same-site requests and some top-level navigations.

Common default.

---

### None

Cookie sent with cross-site requests.

Must also include the `Secure` attribute in modern browsers.

Often used for third-party integrations.

---

## Expires / Max-Age

Defines when the cookie expires.

---

# What is a Session?

A session is **server-side storage** that keeps track of an authenticated user.

Unlike cookies, sessions are stored on the server.

The browser stores only the **session ID**.

Example

Server

```
Session ID

↓

abc123

↓

Rahul

↓

Role = User
```

Browser

```
Cookie

↓

session=abc123
```

The browser never stores the actual user information.

---

# Session Lifecycle

Step 1

User logs in.

↓

Step 2

Server creates a session.

↓

Step 3

Session stored on server.

```
abc123

↓

User Rahul
```

↓

Step 4

Server sends

```http
Set-Cookie: session=abc123
```

↓

Step 5

Browser stores cookie.

↓

Step 6

Every request includes

```http
Cookie: session=abc123
```

↓

Step 7

Server finds session.

↓

User authenticated.

---

# Cookies vs Sessions

| Cookies | Sessions |
|----------|----------|
| Stored in browser | Stored on server |
| Can store user preferences | Stores authenticated user state |
| Browser automatically sends them | Server retrieves session using session ID |
| Can be persistent | Usually expire after inactivity |

---

# Authentication Flow

```
User Login

↓

POST /login

↓

Server verifies credentials

↓

Creates Session

↓

Session ID = abc123

↓

Set-Cookie: session=abc123

↓

Browser stores cookie

↓

GET /profile

↓

Cookie: session=abc123

↓

Server finds session

↓

Returns profile
```

---

# Security Risks

## Session Hijacking

An attacker steals a valid session cookie.

Example

```
Cookie

↓

session=abc123
```

If the attacker uses this cookie, they may impersonate the victim until the session expires.

---

## Session Fixation

The attacker tricks a user into using a known session ID.

After the user logs in, the attacker already knows the session identifier.

---

## Cookie Theft

Possible through

- Cross-Site Scripting (XSS)
- Malware
- Insecure transmission over HTTP

Using the `HttpOnly` and `Secure` attributes helps reduce this risk.

---

## Missing SameSite

Cookies without an appropriate `SameSite` attribute may increase the risk of Cross-Site Request Forgery (CSRF).

---

# Cookies & Sessions in Application Security

Whenever you intercept a request in Burp Suite, inspect:

```http
Cookie: session=abc123
```

Ask yourself:

- Can I modify the session ID?
- Can I remove the cookie?
- Does the application still authenticate me?
- Does logging out invalidate the session?
- Does the session expire?
- Are cookies protected with HttpOnly and Secure?

---

# Common Mistakes

❌ Storing passwords inside cookies.

Cookies should never contain plaintext passwords.

---

❌ Missing HttpOnly.

JavaScript may access cookies if HttpOnly is not set.

---

❌ Missing Secure.

Cookies may be transmitted over HTTP.

---

❌ Long-lived session cookies.

Sessions should expire after inactivity.

---

# Interview Questions

## What is a cookie?

A cookie is a small piece of data stored in the browser that is automatically sent with future requests.

---

## What is a session?

A session is server-side storage used to keep track of authenticated users.

---

## Difference between cookies and sessions?

Cookies are stored in the browser.

Sessions are stored on the server.

The browser stores only the session ID.

---

## Why is HTTP called stateless?

Because the server does not automatically remember previous requests.

Cookies and sessions provide continuity.

---

## What is Session Hijacking?

Using a stolen session ID or cookie to impersonate a legitimate user.

---

## Why is the HttpOnly attribute important?

It prevents JavaScript from reading cookies, reducing the impact of many XSS attacks.

---

## What does the Secure attribute do?

It ensures cookies are only transmitted over HTTPS.

---

## What does SameSite protect against?

It helps reduce the risk of Cross-Site Request Forgery (CSRF).

---

# Burp Suite Observation

When intercepting requests, I should inspect:

- Cookie header
- Set-Cookie response header
- Session ID changes after login
- Cookie attributes (HttpOnly, Secure, SameSite)
- Session expiration
- Logout behavior

---

# AppSec Checklist

Whenever testing authentication:

- Can I reuse another user's session?
- Does logout invalidate the session?
- Is the session regenerated after login?
- Is HttpOnly enabled?
- Is Secure enabled?
- Is SameSite configured?
- Does the session expire after inactivity?
- Can I access protected pages without a valid session?

---

# Key Takeaways

- HTTP is stateless.
- Cookies are stored in the browser.
- Sessions are stored on the server.
- Browsers automatically send cookies with requests.
- Sessions identify authenticated users.
- Cookie security depends on attributes such as HttpOnly, Secure, and SameSite.
- Understanding cookies and sessions is essential before learning CSRF, Session Hijacking, and JWT.

---

# References

- RFC 6265 – HTTP State Management Mechanism
- OWASP Session Management Cheat Sheet
- OWASP Web Security Testing Guide
- PortSwigger Web Security Academy