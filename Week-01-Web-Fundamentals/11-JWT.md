# JSON Web Token (JWT)

> Week 1 - Web Fundamentals
> Application Security Learning Notes

---

# Table of Contents

1. Introduction
2. What is JWT?
3. Why JWT is Used
4. JWT Structure
5. JWT Authentication Flow
6. JWT Header
7. JWT Payload
8. JWT Signature
9. JWT Claims
10. JWT vs Session Authentication
11. JWT in Application Security
12. Common JWT Vulnerabilities
13. Common Mistakes
14. Interview Questions
15. Burp Suite Observation
16. AppSec Checklist
17. My Learning Notes
18. Key Takeaways
19. References

---

# Introduction

Authentication is one of the most important parts of modern web applications.

After a user successfully logs in, the server must identify that user during future requests.

Modern applications commonly achieve this using **JSON Web Tokens (JWTs)**.

JWTs are widely used in:

- REST APIs
- Mobile Applications
- Single Page Applications (SPA)
- Microservices
- OAuth 2.0
- OpenID Connect

As an Application Security Engineer, understanding JWTs is essential because authentication and authorization flaws involving JWTs are common in real-world applications.

---

# What is JWT?

JWT stands for **JSON Web Token**.

A JWT is a compact, URL-safe token used to securely transfer information between a client and a server.

The server creates the JWT after successful authentication.

The client stores the token and sends it with future requests.

The server verifies the token before granting access.

---

# Why JWT is Used

Without JWT

```
User Login

↓

Server verifies credentials

↓

User opens Profile

↓

Server asks:

Who are you?
```

The server has no way to identify the user.

---

With JWT

```
User Login

↓

Server verifies credentials

↓

Creates JWT

↓

Browser stores JWT

↓

Future Requests

↓

Authorization:

Bearer TOKEN

↓

Server verifies JWT

↓

Access Granted
```

JWT allows stateless authentication.

---

# JWT Structure

Every JWT consists of three parts separated by dots.

```
HEADER

.

PAYLOAD

.

SIGNATURE
```

Example

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9

.

eyJ1c2VySWQiOjEwMSwicm9sZSI6InVzZXIifQ

.

RkY3N0M4Yz...
```

```
Header

↓

Payload

↓

Signature
```

---

# JWT Authentication Flow

```
User Login

↓

POST /login

↓

Server verifies username & password

↓

Server generates JWT

↓

JWT returned to browser

↓

Browser stores JWT

↓

Future Requests

↓

Authorization:

Bearer TOKEN

↓

Server verifies signature

↓

Returns Response
```

---

# JWT Header

The Header contains metadata about the token.

Example

```json
{
    "alg":"HS256",
    "typ":"JWT"
}
```

Meaning

```
alg

↓

Signing Algorithm

HS256

typ

↓

JWT
```

Common Algorithms

- HS256
- HS384
- HS512
- RS256
- ES256

---

# JWT Payload

The Payload contains **claims**.

Claims are pieces of information about the user.

Example

```json
{
    "userId":101,
    "name":"Rahul",
    "role":"user"
}
```

Common Claims

| Claim | Purpose |
|---------|----------|
| sub | Subject (User ID) |
| name | Username |
| email | Email Address |
| role | User Role |
| exp | Expiration Time |
| iat | Issued At |
| iss | Issuer |
| aud | Audience |

---

# Important Note

The Payload is **Base64URL encoded**, not encrypted.

Anyone can decode it.

Never store:

- Passwords
- API Keys
- Secrets
- Credit Card Numbers

inside a JWT payload.

---

# JWT Signature

The Signature protects the JWT against modification.

Server creates

```
Header

+

Payload

+

Secret Key

↓

Signature
```

If an attacker modifies the Payload

```
role=user

↓

role=admin
```

The Signature becomes invalid.

The server rejects the token.

---

# JWT Claims

JWT Claims provide information about the authenticated user.

Example

```json
{
    "sub":"101",
    "name":"Rahul",
    "role":"user",
    "exp":1750000000,
    "iat":1749990000
}
```

Important Claims

### sub

Unique identifier.

---

### role

Authorization information.

---

### exp

Expiration timestamp.

Expired JWTs should not be accepted.

---

### iss

Identifies the issuer.

---

### aud

Identifies the intended audience.

---

# JWT vs Session Authentication

| JWT | Session |
|------|----------|
| Stored by client | Session stored on server |
| Stateless | Stateful |
| Sent in Authorization header | Session ID stored in Cookie |
| Server verifies signature | Server looks up session |

---

# JWT in Application Security

Whenever you intercept a JWT

Ask:

Can I decode it?

↓

What claims are present?

↓

Can I modify the Payload?

↓

Will the server accept it?

↓

Can I forge the Signature?

---

Example

Original

```json
{
    "role":"user"
}
```

Modified

```json
{
    "role":"admin"
}
```

Question

Does the server verify the Signature?

---

# Common JWT Vulnerabilities

---

## Weak Secret Key

If the server signs JWTs using a weak secret

```
password123
```

Attackers may guess the secret and generate valid tokens.

---

## Missing Signature Verification

If the server trusts the Payload without verifying the Signature,

attackers can modify claims.

---

## alg=none Attack

Example

```json
{
    "alg":"none"
}
```

Some vulnerable implementations accepted unsigned JWTs.

Modern JWT libraries should reject them.

---

## Expired Tokens Accepted

JWT

```json
{
    "exp":1750000000
}
```

The server must reject expired tokens.

---

## Sensitive Data in Payload

JWT Payload

```json
{
    "password":"Password123"
}
```

Anyone can decode this.

Sensitive information should never be stored in JWT payloads.

---

# Common Mistakes

❌ Thinking JWT Payload is encrypted.

It is only Base64URL encoded.

---

❌ Storing passwords inside JWT.

Never store secrets in JWT payloads.

---

❌ Assuming JWT cannot be modified.

Attackers can modify Header and Payload.

The Signature determines whether modifications are trusted.

---

# Interview Questions

## What is JWT?

JWT is a compact, URL-safe token used for authentication and authorization.

---

## What are the three parts of JWT?

- Header
- Payload
- Signature

---

## Which part protects JWT from modification?

Signature.

---

## Is JWT Payload encrypted?

No.

It is Base64URL encoded.

---

## What happens if an attacker changes role=user to role=admin?

The Signature becomes invalid.

A properly implemented server rejects the token.

---

## Difference between JWT and Session?

JWT is stateless.

Sessions require server-side storage.

---

# Burp Suite Observation

Whenever I intercept a JWT:

Inspect

- Header
- Payload
- Signature

Decode

- Header
- Payload

Check

- Algorithm
- Claims
- Expiration
- Role
- User ID

Modify

- Role
- User ID
- Expiration

Observe whether the server rejects the modified token.

---

# AppSec Checklist

Whenever testing JWT authentication:

- Can I decode the token?
- Is sensitive information exposed?
- Which algorithm is used?
- Can I modify the Payload?
- Does the server verify the Signature?
- Are expired tokens accepted?
- Is alg=none accepted?
- Is the signing secret weak?
- Can I replay an old JWT?

---

# My Learning Notes

## What I Learned

- JWT is used for authentication and authorization.
- JWT has three parts:
  - Header
  - Payload
  - Signature
- Header and Payload are Base64URL encoded.
- Signature protects integrity.
- Never trust client-controlled JWT claims without verification.

---

## Things I Want to Practice

- Decode JWT Header.
- Decode JWT Payload.
- Modify role=user to role=admin.
- Observe Signature validation.
- Solve PortSwigger JWT Labs.

---

# Key Takeaways

- JWT enables stateless authentication.
- JWT consists of Header, Payload, and Signature.
- Payload is readable and should not contain secrets.
- Signature prevents unauthorized modifications.
- JWT security depends on proper signature verification and secure key management.
- Understanding JWTs is essential for API security testing.

---

# References

- RFC 7519 – JSON Web Token (JWT)
- OWASP JSON Web Token Cheat Sheet
- OWASP API Security Top 10
- PortSwigger Web Security Academy – JWT Labs
- JWT.io Documentation