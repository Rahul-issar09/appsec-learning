# HTTP Headers

> Week 1 - Web Fundamentals  
> Application Security Learning Notes

---

# Table of Contents

1. Introduction
2. What are HTTP Headers?
3. Types of Headers
4. Request Headers
5. Response Headers
6. Common Request Headers
7. Common Response Headers
8. Headers in Authentication
9. Security Headers
10. Headers in Application Security
11. Common Mistakes
12. Interview Questions
13. Key Takeaways
14. References

---

# Introduction

HTTP headers are key-value pairs sent between a client and a server that provide additional information about an HTTP request or response.

Headers contain metadata such as:

- Authentication information
- Browser details
- Content type
- Cookies
- Cache settings
- Security policies

As an Application Security Engineer, headers are one of the first things you inspect when analyzing a request.

---

# What are HTTP Headers?

Headers provide additional information about an HTTP message.

Example Request

```http
GET /profile HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Authorization: Bearer eyJhbGc...
Accept: application/json