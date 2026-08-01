# URL Encoding (Percent Encoding)

> Week 1 - Web Fundamentals
> Application Security Learning Notes

---

# Table of Contents

1. Introduction
2. What is URL Encoding?
3. Why URL Encoding is Needed
4. How URL Encoding Works
5. Common URL Encoded Characters
6. URL Encoding Examples
7. URL Encoding in HTTP Requests
8. URL Encoding in Application Security
9. URL Encoding vs Base64
10. URL Encoding vs Encryption
11. Common Mistakes
12. Interview Questions
13. Burp Suite Observation
14. AppSec Checklist
15. Key Takeaways
16. References

---

# Introduction

URLs can only contain a limited set of characters.

Characters like:

- Space
- @
- &
- ?
- /
- =
- %
- <
- >

have special meanings or are not allowed directly in URLs.

To safely transmit these characters, browsers convert them into a special format called **URL Encoding**.

This process is also known as **Percent Encoding**.

---

# What is URL Encoding?

URL Encoding is the process of converting special characters into a format that can safely be transmitted in a URL.

Each encoded character begins with:

```
%
```

followed by two hexadecimal digits representing the character.

Example

Space

↓

```
%20
```

---

# Why URL Encoding is Needed

Suppose a user searches for

```
Application Security
```

The URL becomes

```
https://example.com/search?q=Application Security
```

This URL contains a space.

Spaces are not valid in URLs.

The browser automatically converts it into

```
https://example.com/search?q=Application%20Security
```

The server decodes `%20` back into a space.

---

# How URL Encoding Works

Original Character

↓

Hexadecimal Value

↓

Percent Sign

↓

Encoded Character

Example

```
@
```

ASCII

```
40
```

Encoded

```
%40
```

---

# Common URL Encoded Characters

| Character | Encoded |
|------------|----------|
| Space | %20 |
| @ | %40 |
| / | %2F |
| ? | %3F |
| = | %3D |
| & | %26 |
| + | %2B |
| : | %3A |
| # | %23 |
| % | %25 |
| < | %3C |
| > | %3E |
| " | %22 |
| ' | %27 |

These are the characters you will see most often while using Burp Suite.

---

# URL Encoding Examples

## Example 1

Original

```
Rahul Issar
```

Encoded

```
Rahul%20Issar
```

---

## Example 2

Original

```
rahul@gmail.com
```

Encoded

```
rahul%40gmail.com
```

---

## Example 3

Original

```
https://example.com/profile?id=101
```

Encoded

```
https%3A%2F%2Fexample.com%2Fprofile%3Fid%3D101
```

---

# URL Encoding in HTTP Requests

Original Request

```http
GET /search?q=Application Security
```

Browser Sends

```http
GET /search?q=Application%20Security
```

Server Receives

```
Application Security
```

---

# URL Encoding in Application Security

URL encoding is extremely common during security testing.

Attack payloads are often URL encoded before reaching the server.

---

## SQL Injection

Original Payload

```
' OR 1=1--
```

Encoded

```
%27%20OR%201%3D1--
```

Decode

```
%27 → '

%20 → Space

%3D → =
```

Result

```
' OR 1=1--
```

---

## Cross-Site Scripting (XSS)

Original

```html
<script>alert(1)</script>
```

Encoded

```
%3Cscript%3Ealert(1)%3C%2Fscript%3E
```

When decoded

```html
<script>alert(1)</script>
```

---

## Path Traversal

Original

```
../../etc/passwd
```

Encoded

```
..%2F..%2Fetc%2Fpasswd
```

Attackers often URL encode payloads to bypass poorly implemented filters.

---

# URL Encoding vs Base64

| URL Encoding | Base64 |
|---------------|---------|
| Makes URLs safe | Converts binary/text into ASCII |
| Uses % followed by hexadecimal | Uses Base64 alphabet |
| Easily reversible | Easily reversible |
| Not encryption | Not encryption |

Example

URL Encoding

```
Rahul Issar

↓

Rahul%20Issar
```

Base64

```
Rahul Issar

↓

UmFodWwgSXNzYXI=
```

---

# URL Encoding vs Encryption

URL Encoding

- Reversible
- No secret key
- Used for safe transmission

Encryption

- Requires a secret key
- Protects confidentiality
- Used to secure sensitive information

---

# Common Mistakes

❌ Thinking URL Encoding provides security.

It only changes the representation of data.

Anyone can decode it.

---

❌ Confusing URL Encoding with Base64.

These serve different purposes.

---

❌ Forgetting to decode intercepted payloads.

Many attack payloads appear harmless until decoded.

---

# Interview Questions

## What is URL Encoding?

URL Encoding is the process of converting special characters into a safe format for transmission in URLs.

---

## Why is URL Encoding required?

Because URLs cannot safely contain every character directly.

Reserved and special characters must be encoded.

---

## Is URL Encoding encryption?

No.

It is simply an encoding mechanism.

---

## What does `%20` represent?

A space.

---

## What does `%40` represent?

The @ symbol.

---

## Why is URL Encoding important for AppSec?

Attack payloads are frequently URL encoded.

Understanding encoding helps security engineers recognize and manipulate malicious payloads.

---

# Burp Suite Observation

Whenever I intercept a request, I should check whether parameters are URL encoded.

Common examples:

```
%20

%40

%3C

%3E

%2F

%27

%3D
```

Burp Suite Decoder can decode these values automatically.

---

# AppSec Checklist

Whenever I see encoded data:

- Can I decode it?
- Does it contain an attack payload?
- Can I modify it?
- Can I double encode it?
- Does the application decode it correctly?
- Can encoding bypass input filters?

---

# Key Takeaways

- URL Encoding is also called Percent Encoding.
- It converts special characters into a URL-safe format.
- Every encoded character begins with `%`.
- URL Encoding does not provide security.
- AppSec engineers frequently decode URL-encoded payloads while testing applications.
- Burp Suite Decoder is commonly used for encoding and decoding values.

---

# References

- RFC 3986 – Uniform Resource Identifier (URI)
- MDN Web Docs – URL Encoding
- OWASP Web Security Testing Guide
- PortSwigger Web Security Academy