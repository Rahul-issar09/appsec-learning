# HTTP Methods

> Week 1 - Web Fundamentals
> Application Security Learning Notes

---

# Table of Contents

1. Introduction
2. What are HTTP Methods?
3. GET
4. POST
5. PUT
6. PATCH
7. DELETE
8. HEAD
9. OPTIONS
10. Safe vs Unsafe Methods
11. Idempotent Methods
12. Comparison Table
13. HTTP Methods in Application Security
14. Common Mistakes
15. Interview Questions
16. Key Takeaways
17. References

---

# Introduction

HTTP methods (also called HTTP verbs) define **what action the client wants the server to perform** on a resource.

Whenever a browser, mobile application, or API communicates with a server, it uses one of these methods.

Examples:

- Retrieve user profile
- Create a new account
- Update a password
- Delete a comment

Each operation uses a specific HTTP method.

Understanding HTTP methods is essential because Application Security Engineers analyze and manipulate these requests to discover security vulnerabilities.

---

# What are HTTP Methods?

Think of a web server as a library.

The client tells the librarian what it wants to do.

| Action | HTTP Method |
|---------|-------------|
| Read a book | GET |
| Add a new book | POST |
| Replace a book | PUT |
| Edit a page in a book | PATCH |
| Remove a book | DELETE |

---

# GET

## Purpose

Retrieve information from the server.

GET should **never modify data**.

---

## Example

```
GET /api/users/101 HTTP/1.1
```

Server Response

```json
{
    "id":101,
    "name":"Rahul",
    "role":"user"
}
```

---

## Characteristics

- Retrieves data
- No request body (usually)
- Parameters are commonly passed in the URL
- Can be cached
- Can be bookmarked

---

## Real Example

```
GET /profile
```

```
GET /products
```

```
GET /orders/15
```

---

## AppSec Perspective

Whenever you see a GET request ask:

- Can I modify the ID?
- Can I access another user's resource?
- Is sensitive information present in URL parameters?
- Can the request be replayed?

Example

Original

```
GET /api/users/101
```

Modified

```
GET /api/users/102
```

Possible Vulnerability:

- IDOR
- Broken Object Level Authorization (BOLA)

---

# POST

## Purpose

Create a new resource.

POST usually contains data in the request body.

---

## Example

```
POST /api/users
```

```json
{
    "name":"Rahul",
    "email":"rahul@gmail.com"
}
```

Response

```
HTTP/1.1 201 Created
```

---

## Characteristics

- Creates resources
- Request body contains data
- Not idempotent
- Often used for login and registration

---

## Real Examples

```
POST /login
```

```
POST /register
```

```
POST /products
```

---

## AppSec Perspective

Questions to ask:

- Can I bypass authentication?
- Can I inject SQL?
- Can I submit unexpected fields?
- Is rate limiting implemented?
- Can I create an admin account?

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

Possible Vulnerability

Mass Assignment

---

# PUT

## Purpose

Replace an existing resource.

PUT usually expects the **entire resource**.

---

## Example

Current User

```json
{
    "id":101,
    "name":"Rahul",
    "email":"rahul@gmail.com",
    "city":"Raipur"
}
```

PUT Request

```json
{
    "id":101,
    "name":"Rahul",
    "email":"rahul@gmail.com",
    "city":"Bangalore"
}
```

The entire resource is replaced.

---

## Characteristics

- Full replacement
- Idempotent
- Existing resource required

---

## Real Example

```
PUT /api/users/101
```

---

## AppSec Perspective

Questions to ask

- Can I modify protected fields?
- Can I change another user's information?
- Is authorization enforced?

---

# PATCH

## Purpose

Update only specific fields.

Unlike PUT, PATCH does not replace the entire resource.

---

## Example

Current User

```json
{
    "name":"Rahul",
    "city":"Raipur"
}
```

PATCH Request

```json
{
    "city":"Bangalore"
}
```

Only the city changes.

---

## Characteristics

- Partial update
- Small request body
- Existing resource required

---

## AppSec Perspective

PATCH endpoints are commonly tested for:

- Privilege Escalation
- Mass Assignment
- Missing Authorization

Example

Original

```json
{
    "city":"Raipur"
}
```

Modified

```json
{
    "city":"Raipur",
    "role":"admin"
}
```

Question:

Will the server ignore the new field or accept it?

---

# DELETE

## Purpose

Delete an existing resource.

---

## Example

```
DELETE /api/users/101
```

Server

```
204 No Content
```

---

## Characteristics

- Deletes data
- Idempotent
- Permanent in many applications

---

## AppSec Perspective

Questions to ask

Can I delete another user's resource?

Can I delete admin resources?

Can I bypass authorization?

Example

```
DELETE /orders/15
```

↓

```
DELETE /orders/16
```

Possible Vulnerability

Broken Access Control

---

# HEAD

## Purpose

Same as GET but returns only headers.

No response body is returned.

---

## Example

```
HEAD /image.png
```

Useful for

- Checking if a resource exists
- Getting file size
- Checking cache information

---

# OPTIONS

## Purpose

Returns supported HTTP methods.

Example

```
OPTIONS /api/users
```

Response

```
Allow:

GET

POST

PUT

PATCH

DELETE
```

OPTIONS is commonly used during CORS preflight requests.

---

# Safe vs Unsafe Methods

## Safe Methods

Safe methods should not modify server data.

- GET
- HEAD
- OPTIONS

---

## Unsafe Methods

Unsafe methods modify resources.

- POST
- PUT
- PATCH
- DELETE

---

# Idempotent Methods

A method is idempotent if making the same request multiple times produces the same result.

| Method | Idempotent |
|----------|-----------|
| GET | Yes |
| PUT | Yes |
| PATCH | Usually No (depends on implementation) |
| POST | No |
| DELETE | Yes |
| HEAD | Yes |
| OPTIONS | Yes |

Example

```
DELETE /users/10
```

First request

↓

User deleted

Second request

↓

User is already gone

The final state remains the same.

---

# HTTP Method Comparison

| Method | Purpose | Request Body | Idempotent |
|---------|----------|--------------|------------|
| GET | Retrieve | Usually No | Yes |
| POST | Create | Yes | No |
| PUT | Replace | Yes | Yes |
| PATCH | Partial Update | Yes | Usually No |
| DELETE | Remove | Usually No | Yes |
| HEAD | Headers Only | No | Yes |
| OPTIONS | Supported Methods | No | Yes |

---

# HTTP Methods in Application Security

Every HTTP method can expose security vulnerabilities.

Examples

GET

- IDOR
- Information Disclosure

POST

- SQL Injection
- Authentication Bypass
- Mass Assignment

PUT

- Broken Access Control

PATCH

- Privilege Escalation
- Mass Assignment

DELETE

- Unauthorized Resource Deletion

---

# Common Mistakes

❌ Using GET to change server data.

GET should retrieve data only.

---

❌ Assuming PATCH is always secure.

PATCH endpoints often contain authorization flaws.

---

❌ Forgetting authorization checks on DELETE requests.

Deleting another user's data is a common Broken Access Control issue.

---

# Interview Questions

## What is the difference between PUT and PATCH?

PUT replaces the entire resource.

PATCH updates only specified fields.

---

## Which method should be used for login?

POST

Because credentials are submitted in the request body.

---

## Which method is used to retrieve data?

GET

---

## Which methods are considered safe?

GET

HEAD

OPTIONS

---

## Which methods are idempotent?

GET

PUT

DELETE

HEAD

OPTIONS

---

# AppSec Checklist

Whenever you intercept an HTTP request, ask yourself:

- Can I change the HTTP method?
- Can I replay the request?
- Can I modify resource IDs?
- Can I access another user's resources?
- Can I add unexpected JSON fields?
- Is authorization enforced?
- Is input validation implemented?

---

# Key Takeaways

- HTTP methods define the action requested by the client.
- GET retrieves data.
- POST creates resources.
- PUT replaces resources.
- PATCH updates part of a resource.
- DELETE removes resources.
- Every HTTP method can expose security vulnerabilities if authorization or input validation is missing.

---

# References

- RFC 9110 - HTTP Semantics
- OWASP Web Security Testing Guide
- PortSwigger Web Security Academy