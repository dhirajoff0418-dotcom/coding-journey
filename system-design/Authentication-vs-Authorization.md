# Authentication vs Authorization

## Authentication

Authentication answers:

"Who are you?"

Example:

Username + Password

Output:

Identity verified.

---

## Authorization

Authorization answers:

"What can you access?"

Example:

Admin User

Can access:

- Dashboard
- User Management

Normal User:

- Dashboard only

---

## Difference

| Authentication | Authorization |
|---------------|---------------|
| Who are you? | What can you access? |
| Login process | Permission check |
| Happens first | Happens after authentication |

---

# JWT

JSON Web Token

Used for stateless authentication.

Format:

Header.Payload.Signature

Example:

xxxxx.yyyyy.zzzzz

---

## Header

Contains:

{
 "alg":"HS256",
 "typ":"JWT"
}

---

## Payload

Contains claims.

Example:

{
 "userId":"123",
 "role":"Admin"
}

---

## Signature

Generated using:

Header + Payload + Secret Key

Used to verify token integrity.

---

## Stateless Authentication

Traditional Session:

Server stores session.

JWT:

Client stores token.

Server verifies token only.

No session storage required.

---

## JWT Flow

Login
↓
Generate JWT
↓
Client Stores Token
↓
Send Token in Every Request
↓
Server Validates Token
↓
Response