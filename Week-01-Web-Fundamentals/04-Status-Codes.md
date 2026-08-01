# HTTP Status Codes

> Week 1 - Web Fundamentals
> Application Security Learning Notes

---

# Table of Contents

1. Introduction
2. What are HTTP Status Codes?
3. Status Code Categories
4. 1xx - Informational
5. 2xx - Success
6. 3xx - Redirection
7. 4xx - Client Errors
8. 5xx - Server Errors
9. Status Codes in Application Security
10. Common Mistakes
11. Interview Questions
12. Key Takeaways
13. References

---

# Introduction

Whenever a client sends an HTTP request, the server returns a response.

One of the most important parts of that response is the **HTTP Status Code**.

The status code tells the client whether the request:

- Succeeded
- Failed
- Was redirected
- Was unauthorized
- Encountered a server error

Application Security Engineers constantly monitor status codes while testing applications because they reveal how the application processes requests.

---

# What are HTTP Status Codes?

HTTP status codes are **three-digit numbers** returned by a web server that indicate the outcome of an HTTP request.

Example

```
GET /profile HTTP/1.1
```

Server Response

```
HTTP/1.1 200 OK
```

The number **200** indicates that the request was successful.

---

# Status Code Categories

| Range | Meaning |
|--------|---------|
| 1xx | Informational |
| 2xx | Success |
| 3xx | Redirection |
| 4xx | Client Error |
| 5xx | Server Error |

Remember:

```
1 → Information

2 → Success

3 → Redirect

4 → Client Mistake

5 → Server Mistake
```

---

# 1xx - Informational

These responses indicate that the request has been received and the process is continuing.

These are rarely encountered during normal web application testing.

---

## 100 Continue

The client can continue sending the request.

---

## 101 Switching Protocols

The server agrees to switch protocols.

Example:

HTTP → WebSocket

---

# 2xx - Success

These indicate that the request was processed successfully.

---

## 200 OK

The most common status code.

Example

```
GET /profile
```

Response

```
200 OK
```

Meaning:

The request completed successfully.

---

## 201 Created

Returned when a new resource is created.

Example

```
POST /users
```

Response

```
201 Created
```

---

## 202 Accepted

The request has been accepted but processing has not yet finished.

Commonly used in asynchronous operations.

---

## 204 No Content

The request succeeded but no response body is returned.

Common after DELETE requests.

Example

```
DELETE /users/101
```

↓

```
204 No Content
```

---

# 3xx - Redirection

These tell the client to access a different location.

---

## 301 Moved Permanently

Permanent redirect.

Example

```
http://example.com
```

↓

```
https://example.com
```

Search engines update their indexes.

---

## 302 Found

Temporary redirect.

Example

```
/dashboard
```

↓

```
/login
```

The resource has temporarily moved.

---

## 304 Not Modified

Used with browser caching.

The browser already has the latest version.

The server does not send the content again.

---

# 4xx - Client Errors

The problem is caused by the client's request.

---

## 400 Bad Request

The request is malformed.

Examples:

- Invalid JSON
- Missing required parameters
- Incorrect syntax

---

## 401 Unauthorized

Authentication is required.

Example

```
GET /profile
```

Without JWT

↓

```
401 Unauthorized
```

Meaning:

You are not authenticated.

---

## 403 Forbidden

You are authenticated but do not have permission.

Example

User

↓

Attempts to access

```
/admin
```

↓

```
403 Forbidden
```

---

## 404 Not Found

The requested resource does not exist.

Example

```
GET /users/999999
```

↓

```
404 Not Found
```

---

## 405 Method Not Allowed

The endpoint exists.

The HTTP method is incorrect.

Example

```
DELETE /login
```

↓

```
405 Method Not Allowed
```

---

## 409 Conflict

The request conflicts with the current state.

Example

Creating a user with an email address that already exists.

---

## 429 Too Many Requests

Rate limit exceeded.

Often seen during brute-force protection.

---

# 5xx - Server Errors

These indicate that something went wrong on the server.

---

## 500 Internal Server Error

Unexpected server error.

Common causes:

- Unhandled exceptions
- Programming errors
- Database failures

---

## 502 Bad Gateway

One server received an invalid response from another server.

Common in reverse proxy setups.

---

## 503 Service Unavailable

The server is temporarily unavailable.

Possible reasons:

- Maintenance
- High traffic
- Server overload

---

## 504 Gateway Timeout

The upstream server took too long to respond.

---

# Status Codes in Application Security

Status codes reveal valuable information during security testing.

---

## Authentication Testing

Without JWT

```
GET /profile
```

↓

```
401 Unauthorized
```

Good.

---

## Authorization Testing

Logged in as normal user

↓

Access

```
/admin
```

↓

```
403 Forbidden
```

Authorization is enforced.

---

## IDOR Testing

```
GET /users/102
```

Response

```
200 OK
```

You should not have access.

Possible vulnerability:

- IDOR
- Broken Access Control

---

## SQL Injection

Suppose you send:

```
' OR 1=1--
```

If the application returns:

```
500 Internal Server Error
```

This may indicate that your payload caused a backend exception.

It does **not** confirm SQL Injection, but it suggests the application may not be handling input safely.

---

## Brute Force Testing

After many failed login attempts

↓

```
429 Too Many Requests
```

Good sign.

Rate limiting exists.

---

# Common Mistakes

❌ Thinking 401 and 403 are the same.

401

↓

Not authenticated.

403

↓

Authenticated but not authorized.

---

❌ Assuming every 500 error means SQL Injection.

500 only indicates a server-side error.

Further investigation is required.

---

❌ Assuming 404 always means the resource does not exist.

Some applications intentionally return 404 instead of 403 to hide sensitive resources.

---

# Interview Questions

## What is a status code?

A three-digit number indicating the outcome of an HTTP request.

---

## Difference between 401 and 403?

401

Authentication required.

403

Authenticated but permission denied.

---

## Which status code indicates successful resource creation?

201 Created

---

## Which status code usually follows a successful DELETE request?

204 No Content

---

## Which status code indicates rate limiting?

429 Too Many Requests

---

## Which status code indicates an internal server error?

500 Internal Server Error

---

# Burp Suite Observation

Whenever I intercept a request, I should check:

- Did the response return 200?
- Should it have returned 401?
- Should it have returned 403?
- Did my payload cause a 500?
- Did repeated requests trigger 429?
- Was I redirected using 301 or 302?

Status codes often reveal whether authentication, authorization, or input validation is functioning correctly.

---

# AppSec Checklist

Whenever testing an endpoint ask:

- Did I receive the expected status code?
- Can I bypass authentication?
- Can I turn a 403 into a 200?
- Can I trigger a 500?
- Does rate limiting return 429?
- Is information leaked through different status codes?

---

# Key Takeaways

- Every HTTP response contains a status code.
- Status codes indicate the outcome of a request.
- 2xx means success.
- 3xx means redirection.
- 4xx means client-side errors.
- 5xx means server-side errors.
- Application Security Engineers rely on status codes to understand application behavior and identify potential vulnerabilities.

---

# References

- RFC 9110 – HTTP Semantics
- OWASP Web Security Testing Guide
- PortSwigger Web Security Academy