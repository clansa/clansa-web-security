# DNS (Domain Name System)

DNS stands for Domain Name System. People access websites online using domain names like `apple.com` or `clansawebsec.com`. When you go to `apple.com`, your machine sends a request to DNS to find the IP address behind that domain — because your machine has no idea what "apple.com" actually means on its own.

> **Think of it like this:** "216.58.214.142" is Google.com. You can visit both and end up in the exact same place. It's like your national ID number vs your name — same person, two different ways to identify you. Nobody calls you by your 11-digit ID. They use your name. DNS does the same thing for websites.

---

## There Are 4 DNS Servers Involved in Loading a Website

| Server | Job |
|--------|-----|
| DNS Recursor | Finds the IP address on your behalf |
| Root Nameserver | Points to the right TLD |
| TLD Nameserver | Points to the right Auth server |
| Authoritative Nameserver | Holds the actual IP address |

### DNS Recursor
It receives your request and goes hunting for the IP address. Think of it as a librarian — you walk in and say "I want apple.com" and it says "okay, let me go find that for you." It doesn't know the answer itself, it just knows how to find it.

### Root Nameserver
Doesn't know your specific IP, but knows where all `.com`, `.net`, `.org` domains live. It points the recursor in the right direction.

### TLD Nameserver
Knows who *does* know the IP. It tells the recursor which Authoritative Nameserver is responsible for the domain you want.

### Authoritative Nameserver
The final stop. It holds all the DNS records for the domain. The source of truth. Nothing is more authoritative than the Authoritative Nameserver.

The full path: DNS Recursor → Root Nameserver → TLD Nameserver → Authoritative Nameserver
---

## What Are the Steps in a DNS Lookup?

There are 8 steps, assuming nothing is cached:

1. You type `babilcam.com`. The recursor receives the request.
2. Recursor goes to the Root Nameserver.
3. Root Nameserver points recursor to the `.com` TLD Nameserver.
4. Recursor goes to the `.com` TLD Nameserver.
5. TLD Nameserver points recursor to the Authoritative Nameserver.
6. Recursor goes to the Authoritative Nameserver.
7. Recursor gets the IP address.
8. Recursor delivers the IP address back to you.

After these 8 steps, your browser takes over:

9. Browser sends an `HTTP/S` request — usually a `GET /` to that IP.
10. Server responds with `HTML`, `CSS`, `JavaScript` — the website loads.

---

## Types of DNS Queries

**Recursive Query**
The client asks for an IP and expects a definitive answer — either the IP or an error. No in-betweens.

**Iterative Query**
If the server has the IP, it returns it. If not, it returns the best referral it has and the resolver continues from there on its own.

**Non-Recursive Query**
The resolver already knows the answer from cache — either its own record or a previous lookup from *any* user.

> **Example:** John visits `clansawebsec.com`. The resolver caches the IP. An hour later you ask for the same domain. The resolver says "John already asked for this, I've got it right here" — and skips all 8 steps.

---

## DNS Caching

The purpose of caching is storing domain information temporarily so websites load faster and server load is reduced. Cache is held for a time defined by **TTL (Time To Live)**.

### Browser DNS Caching
Your browser keeps DNS records in its own memory. Next visit — it skips the DNS lookup entirely and goes straight to the IP.

### OS Level DNS Caching
Your operating system also caches DNS between the browser and the resolver. The lookup order:

1. Browser cache → found? Done.
2. OS cache → found? Done.
3. Resolver has NS records → goes straight to Auth, skips Root and TLD.
4. Resolver has nothing → starts from Root.

---

## Types of DNS Records

| Record | What it does |
|--------|-------------|
| `A` | Maps domain to IPv4 (32-bit) — we're running low on these |
| `AAAA` | Maps domain to IPv6 (128-bit) — essentially unlimited |
| `CNAME` | Redirects one domain/subdomain to another |
| `MX` | Points email to the right mail server |
| `TXT` | Stores text data — used for domain verification, SPF, DKIM |
| `TTL` | Timer on every record — how long resolvers cache it |

---

## Summary

DNS translates domain names into IP addresses. When you type a website:

1. Recursor receives your request
2. Asks Root → TLD → Auth Nameserver
3. Gets the IP address
4. Delivers it to your browser
5. Browser connects and loads the site

The whole system exists so you can type `google.com` instead of `216.58.214.142`. Simple idea, surprisingly deep implementation.