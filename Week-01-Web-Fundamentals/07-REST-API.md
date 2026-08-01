# REST API (Representational State Transfer)

> Week 1 - Web Fundamentals
> Application Security Learning Notes

---

# Table of Contents

1. Introduction
2. What is an API?
3. What is REST?
4. REST API Architecture
5. REST Principles
6. CRUD Operations
7. HTTP Methods in REST APIs
8. REST Request & Response
9. JSON in REST APIs
10. Authentication in REST APIs
11. REST APIs in Application Security
12. Common REST API Vulnerabilities
13. Common Mistakes
14. Interview Questions
15. Burp Suite Observation
16. AppSec Checklist
17. Key Takeaways
18. References

---

# Introduction

Modern applications rarely communicate directly with databases.

Instead, communication happens through APIs.

Examples:

- Mobile Apps
- Banking Applications
- E-commerce Websites
- Social Media Platforms

All of these rely heavily on REST APIs.

As an Application Security Engineer, understanding REST APIs is essential because most security testing today focuses on API endpoints rather than traditional web pages.

---

# What is an API?

API stands for **Application Programming Interface**.

An API allows two applications to communicate with each other.

Example

```
Mobile App

↓

REST API

↓

Backend Server

↓

Database
```

The client sends a request.

The API processes the request.

The server returns a response.

---

# What is REST?

REST stands for

**Representational State Transfer**

REST is an architectural style for designing web services.

A REST API communicates using HTTP and usually exchanges data in JSON format.

---

# REST API Architecture

```
Client

↓

HTTP Request

↓

REST API

↓

Business Logic

↓

Database

↓

REST API

↓

JSON Response

↓

Client
```

---

# REST Principles

A REST API generally follows these principles.

---

## Client-Server Architecture

The client and server are independent.

Client

- Browser
- Mobile App
- Postman

Server

- Backend Application
- Database

---

## Stateless

Every request contains all information required to process it.

The server does not automatically remember previous requests.

Authentication is typically maintained using:

- Sessions
- JWT
- OAuth Tokens

---

## Resource-Based

Everything is treated as a resource.

Examples

```
/users

/products

/orders

/comments
```

Resources are identified using URLs.

---

## Uniform Interface

REST APIs use standard HTTP methods.

Examples

GET

POST

PUT

PATCH

DELETE

---

# CRUD Operations

CRUD represents the four basic operations performed on resources.

| CRUD | HTTP Method | Example |
|-------|-------------|----------|
| Create | POST | POST /users |
| Read | GET | GET /users/101 |
| Update | PUT / PATCH | PATCH /users/101 |
| Delete | DELETE | DELETE /users/101 |

---

# HTTP Methods in REST APIs

## GET

Retrieve information.

Example

```
GET /users/101
```

---

## POST

Create a new resource.

Example

```
POST /users
```

---

## PUT

Replace an existing resource.

Example

```
PUT /users/101
```

---

## PATCH

Update specific fields.

Example

```
PATCH /users/101
```

---

## DELETE

Remove a resource.

Example

```
DELETE /users/101
```

---

# REST Request Example

```
POST /api/login HTTP/1.1

Host: api.example.com

Content-Type: application/json
```

Body

```json
{
    "email":"rahul@gmail.com",
    "password":"Password123"
}
```

---

# REST Response Example

```
HTTP/1.1 200 OK

Content-Type: application/json
```

```json
{
    "token":"eyJhbGc...",
    "role":"user"
}
```

---

# JSON in REST APIs

REST APIs commonly exchange information using JSON.

Example

```json
{
    "id":101,
    "name":"Rahul",
    "email":"rahul@gmail.com"
}
```

JSON is lightweight, readable, and easy to parse.

---

# Authentication in REST APIs

REST APIs commonly use the following authentication methods.

---

## Session Authentication

```
Cookie: session=abc123
```

---

## JWT Authentication

```
Authorization:

Bearer eyJhbGc...
```

---

## Basic Authentication

```
Authorization:

Basic YWRtaW46YWRtaW4=
```

---

## API Keys

```
X-API-Key:

abcd1234
```

Commonly used for machine-to-machine communication.

---

# REST APIs in Application Security

Most security testing involves modifying REST API requests.

Questions to ask:

Can I modify resource IDs?

```
GET /users/101
```

↓

```
GET /users/102
```

Possible Vulnerability

IDOR

---

Can I modify JSON fields?

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

Can I remove authentication?

Original

```
Authorization:

Bearer TOKEN
```

↓

Remove Header

↓

Can I still access the endpoint?

Possible Vulnerability

Broken Authentication

---

Can I change HTTP methods?

Example

```
GET /users/101
```

↓

```
DELETE /users/101
```

Does the server reject the request?

---

# Common REST API Vulnerabilities

---

## Broken Authentication

Authentication can be bypassed.

Example

Removing JWT still allows access.

---

## Broken Authorization

Users access resources they do not own.

Example

```
GET /users/102
```

while authenticated as User 101.

---

## Mass Assignment

Unexpected JSON fields are accepted.

Example

```json
{
    "role":"admin"
}
```

---

## Excessive Data Exposure

The API returns more information than necessary.

Example

```json
{
    "passwordHash":"...",
    "isAdmin":false,
    "salary":50000
}
```

Sensitive information should not be exposed.

---

## Rate Limiting Issues

Unlimited requests allow:

- Brute Force
- Enumeration
- DoS

---

# Common Mistakes

❌ Assuming REST APIs are secure because they use HTTPS.

HTTPS protects data in transit but does not prevent authorization flaws, injection attacks, or business logic vulnerabilities.

---

❌ Trusting client-supplied JSON fields.

The server must validate all user input.

---

❌ Returning unnecessary information.

APIs should return only the minimum data required.

---

# Interview Questions

## What is REST?

REST is an architectural style for designing web services that communicate over HTTP.

---

## What is an API?

An API is an interface that allows applications to communicate.

---

## Which format do REST APIs commonly use?

JSON

---

## Which HTTP method creates a resource?

POST

---

## Difference between PUT and PATCH?

PUT replaces an entire resource.

PATCH updates only specified fields.

---

## What is CRUD?

Create

Read

Update

Delete

---

## Name common REST API vulnerabilities.

- Broken Authentication
- Broken Authorization
- IDOR / BOLA
- Mass Assignment
- Excessive Data Exposure
- Rate Limiting Issues

---

# Burp Suite Observation

Whenever intercepting REST API requests:

Inspect

- URL
- HTTP Method
- JSON Body
- Authorization Header
- Cookies
- Status Code
- Response Body

Try

- Changing IDs
- Modifying JSON
- Removing Authentication
- Adding Unexpected Fields
- Replaying Requests

---

# AppSec Checklist

Whenever testing REST APIs ask:

- Can I access another user's resource?
- Can I modify hidden JSON fields?
- Can I bypass authentication?
- Can I escalate privileges?
- Can I change HTTP methods?
- Can I enumerate resources?
- Is rate limiting enabled?
- Does the API expose sensitive data?

---

# Key Takeaways

- REST APIs are the backbone of modern web applications.
- They communicate using HTTP.
- Most REST APIs exchange data in JSON format.
- REST APIs are stateless.
- Authentication commonly uses Sessions, JWT, API Keys, or Basic Authentication.
- Application Security Engineers spend significant time testing REST APIs using Burp Suite and Postman.

---

# References

- REST Architectural Style (Roy Fielding Dissertation)
- OWASP API Security Top 10
- OWASP Web Security Testing Guide
- PortSwigger Web Security Academy