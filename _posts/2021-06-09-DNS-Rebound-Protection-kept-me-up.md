---
layout: post
title:  "How DNS Rebound Protection gets into your way for local HTTPS secured services"
author: karsten
categories: [ tutorial ]
---
# What this is about
For some while I was looking for a convinient solution to leave behind 1Password and KeePassXC as my central password managers.
The soon to be star on the horizon was **bitwarden_rs** selfhosting option, now renamed to **vaultwarden**.

The goal - a convienient and secure password vault without any connectivity from outside while retaining automatic synchronization with all devices setup.

# What is the issue here
Modern browsers disallow using Web Crypto APIs in insecure contexts - that means without HTTPS.

That's why all tutorials to setup a selfhosted **vaultwarden** service include documentation to spin up another docker instance proving a reverse proxy to establish the HTTPS connection.

To get HTTPS working we are lacking two things right now - a valid domain name and the certificates required.

Tutorials introduced me to [DuckDNS](https://www.duckdns.org/domains) an easy and free to use service to register subdomains to [duckdns.org](https://www.duckdns.org/domains).

The easiest way to obtain certificates nowadays is [Let's Encrypt](https://letsencrypt.org/).

Going the private network way introduces two obstacles, one documented in all tutorials, the other not.

The first obstacle is, that given the fact that no traffic from outside should get through to the local service, we will not be able to use HTTP validation (putting a specific file on our webspace to prove we own the domain).

That is documented and workarounds using DNS challenge (adding a specific TXT record in the DNS zone of the domain to prove it's ours) are introduced.

However, here the touble started and I lost several evenings trying to fix it.

None of the options (caddy with DNS auth module, nginx with separately fetched certificates, not recommended rocket option) did work and created strange results.

Trying to ping, nslookup, traceroute my newly created subdomain from inside and outside my network produced varying results and I thought I found my enemy in [pi-hole](https://pi-hole.net/). So I spent several days trying to 