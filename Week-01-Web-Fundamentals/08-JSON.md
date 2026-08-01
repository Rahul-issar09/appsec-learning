# JSON (JavaScript Object Notation)

> Week 1 - Web Fundamentals
> Application Security Learning Notes

---

# Table of Contents

1. Introduction
2. What is JSON?
3. Why JSON is Used
4. JSON Syntax
5. JSON Data Types
6. JSON Objects
7. JSON Arrays
8. Nested JSON
9. JSON in REST APIs
10. JSON Validation
11. JSON in Application Security
12. Common JSON Vulnerabilities
13. Common Mistakes
14. Interview Questions
15. Burp Suite Observation
16. AppSec Checklist
17. Key Takeaways
18. References

---

# Introduction

Modern web applications exchange data primarily using **JSON (JavaScript Object Notation)**.

Whenever you:

- Login
- Register
- Update your profile
- Place an order
- Call a REST API

the request body is usually sent as JSON.

As an Application Security Engineer, understanding JSON is essential because almost every API request you intercept in Burp Suite will contain JSON.

---

# What is JSON?

JSON (JavaScript Object Notation) is a lightweight text-based format used to exchange data between applications.

JSON represents data as **key-value pairs**.

Example

```json
{
    "name":"Rahul",
    "age":20
}
```

Key

```
name
```

Value

```
Rahul
```

---

# Why JSON is Used

JSON is:

- Lightweight
- Human readable
- Easy to parse
- Language independent
- Supported by almost every programming language

It has become the standard format for REST APIs.

---

# JSON Syntax

A JSON object begins with

```json
{
}
```

Keys must always be enclosed in **double quotes**.

Correct

```json
{
    "name":"Rahul"
}
```

Incorrect

```json
{
    name:"Rahul"
}
```

---

# JSON Data Types

JSON supports six primary data types.

---

## String

```json
{
    "name":"Rahul"
}
```

Always enclosed in double quotes.

---

## Number

```json
{
    "age":20
}
```

Numbers are not enclosed in quotes.

---

## Boolean

```json
{
    "isAdmin":false
}
```

Possible values

```
true

false
```

---

## Object

Objects contain key-value pairs.

```json
{
    "user":{
        "name":"Rahul",
        "role":"user"
    }
}
```

---

## Array

Arrays store multiple values.

```json
{
    "skills":[
        "Python",
        "Go",
        "Burp Suite"
    ]
}
```

---

## Null

Represents the absence of a value.

```json
{
    "phone":null
}
```

---

# JSON Objects

Objects are collections of key-value pairs.

Example

```json
{
    "id":101,
    "name":"Rahul",
    "email":"rahul@gmail.com"
}
```

---

# JSON Arrays

Arrays store multiple values.

Example

```json
{
    "roles":[
        "user",
        "developer",
        "admin"
    ]
}
```

Arrays begin with

```
[
```

and end with

```
]
```

---

# Nested JSON

Objects can contain other objects and arrays.

Example

```json
{
    "user":{
        "id":101,
        "name":"Rahul",
        "address":{
            "city":"Raipur",
            "country":"India"
        },
        "skills":[
            "Python",
            "Go",
            "Burp Suite"
        ]
    }
}
```

Nested JSON is extremely common in REST APIs.

---

# JSON in REST APIs

Login Request

```http
POST /api/login

Content-Type: application/json
```

Body

```json
{
    "email":"rahul@gmail.com",
    "password":"Password123"
}
```

Server Response

```json
{
    "token":"eyJhbGc...",
    "role":"user"
}
```

---

# JSON Validation

Applications should validate every field received from clients.

Example

Correct

```json
{
    "age":20
}
```

Incorrect

```json
{
    "age":"twenty"
}
```

Incorrect data types should be rejected.

---

# JSON in Application Security

JSON is one of the first things an AppSec engineer inspects.

Example

Original Request

```json
{
    "name":"Rahul",
    "city":"Raipur"
}
```

Questions to ask

- Can I modify values?
- Can I add new fields?
- Can I remove required fields?
- Can I change data types?

---

# Common JSON Vulnerabilities

---

## Mass Assignment

Original

```json
{
    "name":"Rahul"
}
```

Modified

```json
{
    "name":"Rahul",
    "role":"admin"
}
```

Question

Will the server accept the new field?

If yes,

Possible Vulnerability

Mass Assignment

---

## Privilege Escalation

Original

```json
{
    "isAdmin":false
}
```

Modified

```json
{
    "isAdmin":true
}
```

Question

Does the server trust client input?

---

## Parameter Tampering

Original

```json
{
    "userId":101
}
```

Modified

```json
{
    "userId":102
}
```

Can another user's data be accessed?

Possible Vulnerability

Broken Access Control / IDOR

---

## Input Validation

Original

```json
{
    "age":20
}
```

Modified

```json
{
    "age":-500
}
```

or

```json
{
    "age":"admin"
}
```

Does the server validate input correctly?

---

## Missing Required Fields

Original

```json
{
    "email":"rahul@gmail.com",
    "password":"Password123"
}
```

Modified

```json
{
    "email":"rahul@gmail.com"
}
```

Does authentication still succeed?

---

# Common Mistakes

❌ Trusting JSON received from clients.

Every field must be validated server-side.

---

❌ Assuming hidden fields cannot be modified.

Attackers can add or modify JSON fields using Burp Suite.

---

❌ Forgetting data type validation.

Applications should reject invalid data types.

---

# Interview Questions

## What is JSON?

JSON is a lightweight text-based data interchange format that represents data as key-value pairs.

---

## Why is JSON widely used?

Because it is lightweight, human readable, and easy to parse.

---

## Name JSON data types.

- String
- Number
- Boolean
- Object
- Array
- Null

---

## What is a nested JSON object?

An object that contains another object or array.

---

## Why is JSON important in Application Security?

Because most REST APIs exchange data using JSON, making it the primary format inspected and modified during API security testing.

---

# Burp Suite Observation

Whenever intercepting JSON requests:

Inspect

- Request Body
- Hidden Fields
- Data Types
- User IDs
- Roles
- Boolean Values
- Nested Objects

Try

- Adding Fields
- Removing Fields
- Changing Values
- Changing Data Types
- Sending Unexpected Data

---

# AppSec Checklist

Whenever testing JSON requests ask:

- Can I add new fields?
- Can I modify existing values?
- Can I change userId?
- Can I change role=user to role=admin?
- Can I remove required fields?
- Can I change data types?
- Is input validation implemented?
- Does the server trust client-supplied JSON?

---

# Key Takeaways

- JSON is the standard format used by REST APIs.
- JSON stores information as key-value pairs.
- JSON supports strings, numbers, booleans, objects, arrays, and null values.
- AppSec engineers frequently manipulate JSON requests using Burp Suite.
- Server-side validation is essential because attackers can modify any JSON field.

---

# References

- RFC 8259 – The JavaScript Object Notation (JSON) Data Interchange Format
- OWASP Web Security Testing Guide
- OWASP API Security Top 10
- PortSwigger Web Security Academy