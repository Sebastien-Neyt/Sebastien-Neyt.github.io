---
title: Virtual Hosting
date: 2024-02-01 12:51
categories: [WEB, FUNDAMENTALS, WEB APPLICATIONS]
tags: [virtual-hosting, ffuf]
---

## What is it?
Also known as a vHost, a virtual host allows multiple sites to exist on a single server. Which makes sense if you're hosting multiple applications and you don't necessarily need a IP address or dedicated hardware for each one.

It's important for us to be able to identify how servers are configured and what applications exist on them, otherwise we may miss out a portion of our attack surface.

---

## IP virtual hosts
IP-based virtual hosting, also known as dedicated IP hosting, is where each site or service has its own unique IP address. The web server is configured to respond to each unique IP address assigned to it and serve the appropriate content. 

Why use IP-based virtual hosting?

1. **SSL/TLS Certificates:** Traditionally, secure websites (HTTPS) required a unique IP address for each certificate. Modern improvements such as Server Name Indication (SNI) have mitigated this, but some old browsers and systems still have issues with name-based hosting and SSL/TLS.

2. **Server Control:** With dedicated IPs, we have greater control over the server configuration.

3. **Reputation:** Since each website on an IP-based virtual host has its own unique IP address, they are somewhat "insulated" from each other. This means that if one website is flagged for spam or blacklisted, it won't affect other sites on the same server.


## Name virtual hosts
Name-based virtual hosting, also known as shared IP hosting, lets us host multiple applications on the same IP address. The web server relies on the domain name in the "Host" header in the request to determine which web application to show.

Why use name-based virtual hosting?

1. **Efficient Use of Resources:** It allows for efficient use of IP addresses, a particularly important factor given the limited availability of IPv4 addresses.

2. **Cost-Effective:** Because many websites can share a single IP, hosting costs are typically lower with name-based virtual hosting. This makes it an attractive option for small businesses and personal websites.


### Enumerating name-based virtual hosts
Often during a pentest we might be present with a default apache screen, or during bug bounty we might want to uncover other web applications hosted on the same server. We can do this using ffuf.

It's likely that a default page or response is sent back when unsuccessful, so make sure to filter those results by using `-fs XX` where XX is the size of the response you want to filter.
```shell
ffuf -u http://<target-ip> -w /path/to/wordlist.txt -H "HOST: FUZZ.target-domain.com" -fs XX
```
