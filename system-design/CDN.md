# CDN

## What is CDN?

Content Delivery Network.

A geographically distributed network of edge servers.

Purpose:

- Reduce latency
- Faster content delivery

---

## How CDN Works

User India
↓
Nearest Edge Server

instead of

User India
↓
US Origin Server

---

## Edge Servers

Store cached copies of:

- Images
- Videos
- CSS
- JavaScript

---

## CDN vs Redis

| Feature | CDN | Redis |
|----------|----------|----------|
| Purpose | Static content delivery | Application caching |
| Location | Edge servers | Application layer |
| Used For | Images, CSS, Videos | DB query results |
| Users | Public content | Application data |

---

## Example

### CDN

Image.jpg

User → CDN Edge

---

### Redis

Product Details

User → App
↓
Redis